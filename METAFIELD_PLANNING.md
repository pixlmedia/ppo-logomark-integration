# Metafield Planning: Shopify Custom App Data Structure

## Overview

This document outlines the metafield namespace and data structure for storing Logo Mark product information and pricing configuration in Shopify. It bridges the gap between Logo Mark's product data (via PromoStandards API) and the custom Shopify pricing app.

**Status:** Phase 2 Planning - Awaiting LogoMark field specification from Michael  
**Namespace:** `logomark_custom` (custom metafield namespace)  
**Created:** Based on PRICING_SYSTEM_ANALYSIS.md findings

---

## Metafield Namespace Structure

### Namespace Definition

```
Namespace: logomark_custom
Display Name: Logo Mark Custom
Description: Logo Mark product pricing, options, and configuration data
```

### Metafield Registry (To Be Registered in Shopify Admin)

All metafields will use namespace `logomark_custom` with the following keys:

---

## 1. LOGOMARK SOURCE DATA FIELDS

### Fields Provided by Logo Mark (via PromoStandards API)

These fields will be populated by the import process from Logo Mark. **PLACEHOLDER VALUES - AWAITING MICHAEL'S FIELD SPECIFICATION**

```
Metafield Key | Data Type | Shopify Type | Purpose | Example
---|---|---|---|---
sku_original | Text | single_line_text_field | Original LogoMark SKU | KP2444B
product_id_lm | Text | single_line_text_field | LogoMark internal product ID | [AWAITING SPEC]
moq | Integer | integer | Minimum order quantity | 150
standard_unit_name | Text | single_line_text_field | Unit of measure | pieces
lead_time_days | Integer | integer | Days to produce | 5
product_class | Text | single_line_text_field | Product category from LogoMark | [AWAITING SPEC]
decorated_flag | Boolean | boolean | Whether product can be decorated | true
blank_price_available | Boolean | boolean | Whether blank (undecorated) option available | true
```

### Fields Awaiting Michael's Specification

The following data structures are anticipated from LogoMark but need confirmation:

- **Option structure**: How are options defined? (location-based, feature-based, etc.)
- - **Pricing structure**: How are quantity breaks provided?
  - - **Color/imprint data**: How are color options and limits encoded?
    - - **Setup charges**: If LogoMark charges setup fees, how are they indicated?
      - - **Decoration methods**: What decoration methods are available per product?
        - - **Surcharge fields**: How are per-unit surcharges defined in LogoMark's system?
         
          - **Action Item:** Review Michael's specification and map these fields before Phase 2 completion.
         
          - ---

          ## 2. CUSTOM PRICING CONFIGURATION FIELDS

          ### These fields are defined by us for the custom app to function

          These fields must be manually configured per product (or set via import if LogoMark provides the data).

          #### Quantity Break Pricing

          **Key:** `quantity_breaks`
          **Type:** JSON
          **Shopify Type:** `json` (or `metaobject_reference` if using metaobjects)
          **Purpose:** Define price tiers by quantity

          ```json
          {
            "quantity_breaks": [
              {
                "min_qty": 150,
                "max_qty": 249,
                "base_price": 4.89,
                "currency": "USD",
                "notes": "Decorated base price"
              },
              {
                "min_qty": 250,
                "max_qty": 499,
                "base_price": 4.59,
                "currency": "USD"
              },
              {
                "min_qty": 500,
                "max_qty": 999,
                "base_price": 4.29,
                "currency": "USD"
              },
              {
                "min_qty": 1000,
                "base_price": 3.99,
                "currency": "USD",
                "notes": "No maximum - applies to 1000+"
              }
            ]
          }
          ```

          **Example in Shopify Admin:**
          - Key: `logomark_custom.quantity_breaks`
          - - Value: `[JSON as above]`
           
            - ---

            #### Options Configuration

            **Key:** `options`
            **Type:** JSON
            **Purpose:** Define product options and their pricing impacts

            ```json
            {
              "options": [
                {
                  "id": "option_decoration",
                  "name": "Decoration",
                  "type": "select",
                  "values": [
                    {
                      "value_id": "decoration_decorated",
                      "label": "Decorated",
                      "per_unit_surcharge": 0,
                      "setup_charge": 0,
                      "is_default": true
                    },
                    {
                      "value_id": "decoration_blank",
                      "label": "Blank",
                      "per_unit_surcharge": -0.49,
                      "setup_charge": 0,
                      "note": "Price reduction for blank items"
                    }
                  ],
                  "display_order": 1,
                  "required": true
                },
                {
                  "id": "option_imprint_colors",
                  "name": "Additional Imprint Colors",
                  "type": "multiselect",
                  "max_selections": 3,
                  "per_unit_surcharge_per_color": 0.10,
                  "setup_charge": 50,
                  "setup_description": "One-time imprint setup charge",
                  "setup_triggered_when": "color_count_exceeds_base",
                  "base_color_count": 1,
                  "display_order": 2,
                  "required": false,
                  "depends_on": {
                    "option_id": "option_decoration",
                    "required_value": "decoration_decorated"
                  }
                },
                {
                  "id": "option_imprint_location",
                  "name": "Imprint Location",
                  "type": "select",
                  "values": [
                    {
                      "value_id": "location_front_center",
                      "label": "Front Center",
                      "per_unit_surcharge": 0,
                      "setup_charge": 0,
                      "is_default": true,
                      "max_colors_allowed": 1
                    },
                    {
                      "value_id": "location_back",
                      "label": "Back",
                      "per_unit_surcharge": 0,
                      "setup_charge": 75,
                      "setup_description": "Back mold setup (one time)",
                      "max_colors_allowed": 1
                    }
                  ],
                  "display_order": 3,
                  "depends_on": {
                    "option_id": "option_decoration",
                    "required_value": "decoration_decorated"
                  }
                }
              ]
            }
            ```

            **Example in Shopify Admin:**
            - Key: `logomark_custom.options`
            - - Value: `[JSON as above]`
             
              - ---

              ## 3. PRICING CALCULATION MAPPING

              ### How Metafields Map to Custom App Logic

              The custom app's pricing engine will read these metafields and calculate prices as follows:

              ```
              1. Customer selects quantity (e.g., 500 units)
                 ↓
              2. App reads `logomark_custom.quantity_breaks`
                 → Finds matching tier: min_qty=500, base_price=$4.29
                 ↓
              3. Customer selects options:
                 - Decoration: "Decorated" (no surcharge)
                 - Imprint Colors: 2 colors (base is 1, so +1 additional)
                 - Imprint Location: "Front Center" (no setup)
                 ↓
              4. App reads `logomark_custom.options` to find surcharges
                 → option_imprint_colors: per_unit_surcharge = $0.10 per color
                 → Applied surcharge: 1 additional color × $0.10 = $0.10/unit
                 ↓
              5. Calculate unit price:
                 base_price ($4.29) + surcharge ($0.10) = $4.39/unit
                 ↓
              6. Apply quantity:
                 $4.39/unit × 500 units = $2,195.00
                 ↓
              7. Check for setup charges:
                 → No setup charges triggered (no additional color = no setup)
                 ↓
              8. Final total: $2,195.00
                 Display breakdown:
                 - Base: $2,145.00 (500 × $4.29)
                 - Color surcharge: $50.00 (500 × $0.10)
                 - Setup charges: $0.00
                 - TOTAL: $2,195.00
              ```

              ---

              ## 4. METAFIELD DATA TYPES & SHOPIFY CONFIGURATION

              ### Metafield Types to Register

              | Metafield Key | Shopify Type | Sample Value | Notes |
              |---|---|---|---|
              | `sku_original` | `single_line_text_field` | `KP2444B` | Max 255 chars |
              | `moq` | `integer` | `150` | Single integer |
              | `quantity_breaks` | `json` | `[{min_qty:..}]` | JSON object |
              | `options` | `json` | `[{id:..}]` | JSON array |
              | `decorated_flag` | `boolean` | `true` | True/false |
              | `lead_time_days` | `integer` | `5` | Integer days |

              ### Shopify Admin Registration

              When Michael's specification arrives, register metafields in Shopify with:

              ```
              Settings → Apps and integrations → Custom apps
              → Configuration
              → Admin API access scopes
              → Check: write_products, read_products (for metafield access)

              Then navigate to:
              Settings → Metafields
              → Products
              → Create metafield (repeat for each key above)
              ```

              ---

              ## 5. EXAMPLE PRODUCT: Die Cast Dog Tag

              ### Complete Metafield Example

              To illustrate how all fields work together, here's the complete metafield configuration for the PK Die Cast Dog Tag (from our analysis):

              ```json
              {
                "sku_original": "PKCUDD-BLK",
                "product_id_lm": "123456",
                "moq": 150,
                "standard_unit_name": "pieces",
                "lead_time_days": 7,
                "product_class": "keychains",
                "decorated_flag": true,
                "blank_price_available": false,

                "quantity_breaks": [
                  { "min_qty": 150, "max_qty": 249, "base_price": 3.48 },
                  { "min_qty": 250, "max_qty": 499, "base_price": 3.29 },
                  { "min_qty": 500, "max_qty": 999, "base_price": 3.11 },
                  { "min_qty": 1000, "max_qty": 2499, "base_price": 3.07 },
                  { "min_qty": 2500, "base_price": 3.04 }
                ],

                "options": [
                  {
                    "id": "option_sides",
                    "name": "Customization Sides",
                    "type": "select",
                    "values": [
                      {
                        "value_id": "sides_one",
                        "label": "One sided (customized on one side)",
                        "per_unit_surcharge": 0,
                        "setup_charge": 0,
                        "is_default": true
                      },
                      {
                        "value_id": "sides_two",
                        "label": "Two sided (customized on both sides)",
                        "per_unit_surcharge": 0.15,
                        "setup_charge": 120,
                        "setup_description": "Mold for front and back side (one time)"
                      }
                    ],
                    "display_order": 1
                  },
                  {
                    "id": "option_colors",
                    "name": "Additional Colors",
                    "type": "multiselect",
                    "max_selections": 4,
                    "values": [
                      {
                        "value_id": "color_1",
                        "label": "1 color added",
                        "per_unit_surcharge": 0.05
                      },
                      {
                        "value_id": "color_2",
                        "label": "2 colors added",
                        "per_unit_surcharge": 0.10
                      },
                      {
                        "value_id": "color_3",
                        "label": "3 colors added",
                        "per_unit_surcharge": 0.15
                      }
                    ],
                    "display_order": 2
                  },
                  {
                    "id": "option_keyring",
                    "name": "Keyring vs Necklace",
                    "type": "select",
                    "values": [
                      {
                        "value_id": "keyring_yes",
                        "label": "Keyring",
                        "per_unit_surcharge": 0,
                        "is_default": true
                      },
                      {
                        "value_id": "keyring_no",
                        "label": "Necklace",
                        "per_unit_surcharge": 0,
                        "note": "No price difference"
                      }
                    ],
                    "display_order": 3
                  }
                ]
              }
              ```

              **For 500 units, two-sided, 1 color:**
              - Base (500 × $3.11): $1,555.00
              - - Two-sided surcharge (500 × $0.15): $75.00
                - - Color surcharge (500 × $0.05): $25.00
                  - - Setup (one-time): $120.00
                    - - **TOTAL: $1,775.00**
                     
                      - ---

                      ## 6. PLACEHOLDER STRUCTURE FOR LOGOMARK DATA

                      ### Fields Still Pending Michael's Specification

                      Until we receive Michael's field specification, these are placeholder structures:

                      ```json
                      {
                        "logomark_provided_fields": {
                          "sku": "PLACEHOLDER",
                          "options": "AWAITING SPEC",
                          "pricing_tiers": "AWAITING SPEC",
                          "setup_charges": "AWAITING SPEC",
                          "decoration_methods": "AWAITING SPEC",
                          "surcharge_details": "AWAITING SPEC"
                        }
                      }
                      ```

                      **Critical Questions for Michael:**

                      1. What is the exact field naming convention for SKUs in PromoStandards API?
                      2. 2. How are product options encoded? (Array? Separate fields?)
                         3. 3. Are quantity breaks provided with each product, or is there a master table?
                            4. 4. Do setup charges come from LogoMark, or must we define them?
                               5. 5. What's the structure for color limits per location?
                                  6. 6. Are per-unit surcharges provided by LogoMark?
                                     7. 7. What's the format for decoration method pricing?
                                       
                                        8. ---
                                       
                                        9. ## 7. IMPLEMENTATION TIMELINE
                                       
                                        10. ### Phase 2: Metafield Planning (Current)
                                        11. - ✅ Define metafield structure (this document)
                                            - - ⏳ Receive LogoMark field specification from Michael
                                              - - ⏳ Map LogoMark fields to Shopify metafield keys
                                                - - ⏳ Document any field transformations needed
                                                 
                                                  - ### Phase 3: Metafield Setup
                                                  - - Register metafields in Shopify Admin
                                                    - - Create metafield schema definitions
                                                      - - Test with example products
                                                       
                                                        - ### Phase 4: Custom App Development
                                                        - - Build app to read these metafields
                                                          - - Implement pricing calculation engine
                                                            - - Handle cascading options and setup charges
                                                             
                                                              - ### Phase 5: Data Import
                                                              - - Create import process to populate metafields
                                                                - - Load test data
                                                                  - - Validate calculations against manual pricing
                                                                   
                                                                    - ---

                                                                    ## 8. Best Practices

                                                                    ### Do's
                                                                    ✅ Use consistent naming (snake_case for keys)
                                                                    ✅ Include setup descriptions in setup_charge fields
                                                                    ✅ Mark default options with `is_default: true`
                                                                    ✅ Document surcharge triggers and dependencies
                                                                    ✅ Keep JSON well-formatted for readability
                                                                    ✅ Use meaningful value_ids that describe the option

                                                                    ### Don'ts
                                                                    ❌ Don't store unstructured pricing data
                                                                    ❌ Don't mix currencies without explicit field
                                                                    ❌ Don't nest more than 3 levels deep in JSON
                                                                    ❌ Don't use spaces in keys (use snake_case)
                                                                    ❌ Don't store redundant data (calculate instead)

                                                                    ---

                                                                    ## 9. Next Steps

                                                                    1. **Receive LogoMark Field Spec** - Michael provides exact field structure
                                                                    2. 2. **Map Fields** - Create mapping document between LogoMark ↔ Shopify metafields
                                                                       3. 3. **Update This Document** - Replace placeholders with actual field names
                                                                          4. 4. **Register Metafields** - Set up in Shopify Admin
                                                                             5. 5. **Build Import Logic** - Create SOAP client code to populate metafields
                                                                                6. 6. **Test with Real Data** - Load sample products and verify calculations
                                                                                  
                                                                                   7. ---
                                                                                  
                                                                                   8. ## References
                                                                                  
                                                                                   9. - PRICING_SYSTEM_ANALYSIS.md - Product pricing patterns analysis
                                                                                      - - SHOPIFY_PRICING_ARCHITECTURE.md - Custom app architecture decision
                                                                                        - - PromoStandards API Documentation - (To be provided by Michael)
                                                                                          - - Shopify Metafields API - https://shopify.dev/api/admin-rest/2024-01/resources/metafield
