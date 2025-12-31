# Shopify Metafield Setup: Complete Guide

## Status: Phase 3 - In Progress

✅ **Metafield 1 Created:** `logomark_custom.sku_original` (Single line text)

---

## Summary

This guide provides step-by-step instructions to create the remaining 7 metafields in your Shopify Admin. The first metafield has been successfully created as a working example. Follow the same process for fields 2-8 below.

---

## Metafields to Create (Copy-Paste Ready)

Use this table as your checklist. Navigate to **Settings → Metafields → Products → Add definition** for each field.

| # | Name | Type | Status |
|---|------|------|--------|
| 1 | `logomark_custom.sku_original` | Single line text | ✅ DONE |
| 2 | `logomark_custom.product_id_lm` | Single line text | ⏳ TODO |
| 3 | `logomark_custom.moq` | Integer | ⏳ TODO |
| 4 | `logomark_custom.lead_time_days` | Integer | ⏳ TODO |
| 5 | `logomark_custom.quantity_breaks` | JSON | ⏳ TODO |
| 6 | `logomark_custom.options` | JSON | ⏳ TODO |
| 7 | `logomark_custom.decorated_flag` | Boolean | ⏳ TODO |
| 8 | `logomark_custom.blank_price_available` | Boolean | ⏳ TODO |

---

## Step-by-Step Instructions

### How to Create Each Metafield

1. **Navigate to Shopify Admin**
2.    - Go to Settings → Metafields → Products
      -    - Click "Add definition" (top right)
       
           - 2. **For Each Metafield:**
             3.    - Copy the name from the table above
                   -    - Paste into the "Name" field
                        -    - Click the "Type" dropdown
                             -    - Select the type from the table
                                  -    - Click "Save"
                                       -    - Repeat for the next metafield
                                        
                                            - ---

                                            ## Detailed Instructions for Each Type

                                            ### Single Line Text (Fields 2)
                                            - **Type:** Single line text
                                            - - **Location:** Recommended section in Type dropdown
                                              - - **Settings:** Leave all options default
                                                - - **Count:** 1 field (product_id_lm)
                                                 
                                                  - ### Integer (Fields 3-4)
                                                  - - **Type:** Integer
                                                    - - **Location:** Recommended section in Type dropdown
                                                      - - **Settings:** Leave all options default
                                                        - - **Count:** 2 fields (moq, lead_time_days)
                                                         
                                                          - ### JSON (Fields 5-6)
                                                          - - **Type:** JSON (scroll down in Type dropdown)
                                                            - - **Location:** Under "Other" or "Advanced" section
                                                              - - **Settings:** Leave all options default
                                                                - - **Count:** 2 fields (quantity_breaks, options)
                                                                  - - **Note:** These will store the complex pricing data
                                                                   
                                                                    - ### Boolean (Fields 7-8)
                                                                    - - **Type:** Boolean (look in Type dropdown)
                                                                      - - **Location:** Under "Options" or "True/False" section
                                                                        - - **Settings:** Leave all options default
                                                                          - - **Count:** 2 fields (decorated_flag, blank_price_available)
                                                                           
                                                                            - ---

                                                                            ## Field Descriptions (Optional)

                                                                            You can optionally add descriptions in Shopify to make it easier for your team. Here are suggested descriptions:

                                                                            | Field Name | Description |
                                                                            |---|---|
                                                                            | `sku_original` | Original LogoMark SKU identifier |
                                                                            | `product_id_lm` | LogoMark internal product ID |
                                                                            | `moq` | Minimum order quantity for this product |
                                                                            | `lead_time_days` | Number of days to produce |
                                                                            | `quantity_breaks` | JSON array of quantity price tiers: [{min_qty, max_qty, base_price}] |
                                                                            | `options` | JSON array of product options with surcharges and setup charges |
                                                                            | `decorated_flag` | Whether this product supports decoration |
                                                                            | `blank_price_available` | Whether this product has pricing for blank (undecorated) version |

                                                                            ---

                                                                            ## Verification Checklist

                                                                            After creating all 8 metafields, verify in Shopify:

                                                                            - [ ] Navigate back to **Settings → Metafields → Products**
                                                                            - [ ] - [ ] Search for "logomark_custom" in the definition list
                                                                            - [ ] - [ ] Confirm all 8 metafields appear
                                                                            - [ ] - [ ] Verify each has the correct type displayed
                                                                            - [ ] - [ ] Check the count increases: 36 → 44 metafields total
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## Working Example: logomark_custom.sku_original
                                                                           
                                                                            - [ ] The first metafield has been created successfully. When you create the others, they'll follow the same pattern:
                                                                           
                                                                            - [ ] ```
                                                                            - [ ] Name: logomark_custom.sku_original
                                                                            - [ ] Type: Single line text
                                                                            - [ ] Namespace: custom
                                                                            - [ ] Key: logomark_custom_sku_original
                                                                            - [ ] (Shopify auto-generates the key from the name)
                                                                            - [ ] ```
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## What Happens After All Metafields Are Created
                                                                           
                                                                            - [ ] Once all 8 metafields are created:
                                                                           
                                                                            - [ ] 1. **Metafields will be available** on all Product pages in Shopify Admin
                                                                            - [ ] 2. **Your custom app** will be able to read/write these fields via the Admin API
                                                                            - [ ] 3. **Data import process** can populate them with Logo Mark product information
                                                                            - [ ] 4. **Pricing calculations** will reference these fields to calculate dynamic prices
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## Common Issues & Troubleshooting
                                                                           
                                                                            - [ ] ### Issue: Name field won't accept "logomark_custom."
                                                                           
                                                                            - [ ] **Solution:** This is fine - Shopify allows periods in metafield names. Just paste the full name as provided.
                                                                           
                                                                            - [ ] ### Issue: Can't find "JSON" in Type dropdown
                                                                           
                                                                            - [ ] **Solution:** Scroll down in the Type dropdown. JSON is listed under "Data types" section. It may require scrolling the dropdown menu.
                                                                           
                                                                            - [ ] ### Issue: Metafield appears with different name in Admin
                                                                           
                                                                            - [ ] **Solution:** Shopify sometimes displays it differently than you entered it. Check the "key" field - it should contain the information you entered.
                                                                           
                                                                            - [ ] ### Issue: "Save" button is disabled
                                                                           
                                                                            - [ ] **Solution:** Make sure you've selected a Type from the dropdown. All required fields are: Name + Type.
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## Timeline
                                                                           
                                                                            - [ ] Estimated time to complete:
                                                                            - [ ] - **Time per metafield:** 1-2 minutes (click, type, select, save)
                                                                            - [ ] - **Total for 7 fields:** 10-15 minutes
                                                                            - [ ] - **Buffer for any issues:** 20 minutes total
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## Next Phase
                                                                           
                                                                            - [ ] After metafields are created:
                                                                            - [ ] - [ ] Document completion with screenshot of all 8 in the metafields list
                                                                            - [ ] - [ ] Begin implementing Logo Mark data import process
                                                                            - [ ] - [ ] Build custom app to read these metafields
                                                                            - [ ] - [ ] Test with sample product data
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## Quick Copy-Paste Reference
                                                                           
                                                                            - [ ] If you just want to rapidly enter the names, here they are in order:
                                                                           
                                                                            - [ ] ```
                                                                            - [ ] logomark_custom.sku_original
                                                                            - [ ] logomark_custom.product_id_lm
                                                                            - [ ] logomark_custom.moq
                                                                            - [ ] logomark_custom.lead_time_days
                                                                            - [ ] logomark_custom.quantity_breaks
                                                                            - [ ] logomark_custom.options
                                                                            - [ ] logomark_custom.decorated_flag
                                                                            - [ ] logomark_custom.blank_price_available
                                                                            - [ ] ```
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] ## References
                                                                           
                                                                            - [ ] - METAFIELD_PLANNING.md - Detailed metafield structure and design
                                                                            - [ ] - Shopify Metafields Documentation - https://help.shopify.com/en/manual/custom-data/metafields/
                                                                            - [ ] - Admin Custom Data API - https://shopify.dev/api/admin-rest/2024-01/resources/metafield
                                                                           
                                                                            - [ ] ---
                                                                           
                                                                            - [ ] **Created:** Phase 3 - Metafield Infrastructure Setup
                                                                            - [ ] **Status:** In Progress - 1 of 8 fields created
