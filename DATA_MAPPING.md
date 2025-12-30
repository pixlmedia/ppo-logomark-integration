# PPO Logo Mark Integration - Data Mapping Specification

## Overview
This document defines the transformation rules for mapping Logo Mark SOAP API responses to PPO Shopify product data, including field mapping, data transformations, and validation rules.

**Status**: Template - to be completed when Logo Mark field specification Excel is received from Michael.

## 1. SKU Transformation (Complete)

### Rule: ASCII Character Shift (+1)
Each character in the Logo Mark SKU is shifted right by 1 in ASCII code, with wrapping:
- A → B, B → C, ... Z → A
- - a → b, b → c, ... z → a
  - - 0 → 1, 1 → 2, ... 9 → 0
    - - Special characters: unchanged (if any)
     
      - ### Example
      - ```
        Logo Mark SKU:  PKACBB
        Transform:      P→Q, K→L, A→B, C→D, B→C, B→C
        PPO SKU:        QLBDCC
        ```

        ### Implementation Checklist
        - [ ] Validate input SKU (alphanumeric, 1-20 chars)
        - [ ] - [ ] Apply character-by-character transformation
        - [ ] - [ ] Handle edge cases (Z→A, 9→0)
        - [ ] - [ ] Return transformed SKU
        - [ ] - [ ] Log transformation for audit trail
       
        - [ ] ### Code Template (Node.js)
        - [ ] ```javascript
        - [ ] function transformSKU(logomarkSKU) {
        - [ ]   return logomarkSKU
        - [ ]       .split('')
        - [ ]       .map(char => {
        - [ ]         if (/[A-Z]/.test(char)) {
        - [ ]             return char === 'Z' ? 'A' : String.fromCharCode(char.charCodeAt(0) + 1);
        - [ ]               }
        - [ ]                 if (/[a-z]/.test(char)) {
        - [ ]                     return char === 'z' ? 'a' : String.fromCharCode(char.charCodeAt(0) + 1);
        - [ ]                       }
        - [ ]                         if (/[0-9]/.test(char)) {
        - [ ]                             return char === '9' ? '0' : String.fromCharCode(char.charCodeAt(0) + 1);
        - [ ]                               }
        - [ ]                                 return char; // Special chars unchanged
        - [ ]                                 })
        - [ ]                                 .join('');
        - [ ]                             }
       
        - [ ]                         // Test: transformSKU('PKACBB') → 'QLBDCC'
        - [ ]                     ```
       
        - [ ]                 ---
       
        - [ ]             ## 2. Product Data Mapping (TBD - Awaiting Logo Mark Spec)
       
        - [ ]         ### Expected Fields from Product Data 2.0 API
        - [ ]     ```
        - [ ] Logo Mark Field          PPO Field              Transformation
        - [ ] ─────────────────────────────────────────────────────────────
        - [ ] [TBD]                    product.title          [TBD]
        - [ ] [TBD]                    product.bodyHtml       AI Enhancement
        - [ ] [TBD]                    product.sku            SKU Shift
        - [ ] [TBD]                    product.vendorCode     Hardcode: "LogoMark"
        - [ ] [TBD]                    metafield.moq           [TBD]
        - [ ] [TBD]                    metafield.logomark_id  [TBD]
        - [ ] ```
       
        - [ ] ### Placeholder Template (to be filled when spec received)
        - [ ] ```json
        - [ ] {
        - [ ]   "productId": "LOGO_MARK_ID",
        - [ ]     "name": "Product Name",
        - [ ]   "description": "Original description",
        - [ ]     "sku": "PKACBB",
        - [ ]   "moq": 50,
        - [ ]     "category": "Belt Buckles",
        - [ ]   "images": ["url1", "url2"],
        - [ ]     "pricing": {
        - [ ]     "quantityBreaks": [
        - [ ]       { "qty": 10, "price": 5.00 },
        - [ ]         { "qty": 25, "price": 4.50 },
        - [ ]           { "qty": 50, "price": 4.00 }
        - [ ]           ]
        - [ ]         },
        - [ ]       "variants": [
        - [ ]       {
        - [ ]         "name": "Size",
        - [ ]           "values": ["2x3", "3x4"]
        - [ ]           }
        - [ ]         ]
        - [ ]     }
        - [ ] ```
       
        - [ ] ---
       
        - [ ] ## 3. Price Mapping (Awaiting Spec)
       
        - [ ] ### Expected Input from Product Pricing & Config 1.0 API
        - [ ] ```
        - [ ] Quantity Break: [10, 25, 50, 100, 250, 500]
        - [ ] Base Prices:    [5.00, 4.75, 4.50, 4.25, 4.00, 3.75]
        - [ ] ```
       
        - [ ] ### PPO Output: Tablepress Table Structure
        - [ ] ```
        - [ ] MOQ = 50
        - [ ] Size \ Qty      10      25      50      100     250     500
        - [ ] ────────────────────────────────────────────────────────────
        - [ ] 2x3             -       -       4.50    4.25    4.00    3.75
        - [ ] 3x4             -       -       5.00    4.75    4.50    4.25
        - [ ] 5x7             -       -       6.00    5.75    5.50    5.25
        - [ ] ```
       
        - [ ] ### Transformation Logic
        - [ ] 1. Identify MOQ from Product Data 2.0 (e.g., 50)
        - [ ] 2. Filter quantity breaks: only show breaks >= MOQ
        - [ ] 3. Retrieve pricing from Product Pricing & Config 1.0
        - [ ] 4. Add Easify surcharges per option value
        - [ ] 5. Generate Tablepress table HTML/JSON
       
        - [ ] ### Example Calculation
        - [ ] ```
        - [ ] Logo Mark Data:
        - [ ] - MOQ: 50
        - [ ] - Base price at qty 50: $4.50
        - [ ] - Base price at qty 100: $4.25
       
        - [ ] Easify Option Surcharge:
        - [ ] - Size "2x3": +$0
        - [ ] - Size "3x4": +$0.50
       
        - [ ] PPO Final Price:
        - [ ] - Size 2x3, Qty 50: $4.50 + $0 = $4.50
        - [ ] - Size 3x4, Qty 50: $4.50 + $0.50 = $5.00
        - [ ] ```
       
        - [ ] ### Validation Checklist
        - [ ] - [ ] MOQ >= 10
        - [ ] - [ ] Quantity breaks in ascending order
        - [ ] - [ ] Prices in descending order (lower qty = higher price)
        - [ ] - [ ] No price gaps > 20% between breaks (warn if true)
        - [ ] - [ ] All Easify surcharges are positive numbers
       
        - [ ] ---
       
        - [ ] ## 4. Media / Image Mapping
       
        - [ ] ### Expected Input from Media Content 1.1 API
        - [ ] ```
        - [ ] {
        - [ ]   "images": [
        - [ ]       {
        - [ ]         "url": "https://logomark.com/products/PKACBB/image1.jpg",
        - [ ]           "type": "PRODUCT_IMAGE",
        - [ ]             "sequence": 1
        - [ ]             },
        - [ ]             {
        - [ ]               "url": "https://logomark.com/products/PKACBB/image2.jpg",
        - [ ]                 "type": "PRODUCT_IMAGE",
        - [ ]                   "sequence": 2
        - [ ]                   }
        - [ ]                 ]
        - [ ]             }
        - [ ]         ```
       
        - [ ]     ### PPO Output: Shopify Media API
        - [ ] ```
        - [ ] POST /admin/api/2024-01/graphql.json
       
        - [ ] mutation CreateProductImage {
        - [ ]   productImageCreate(input: {
        - [ ]       productId: "gid://shopify/Product/123"
        - [ ]       image: {
        - [ ]         src: "https://logomark.com/products/PKACBB/image1.jpg"
        - [ ]         }
        - [ ]       }) {
        - [ ]       productImage {
        - [ ]         id
        - [ ]           url
        - [ ]           }
        - [ ]         }
        - [ ]     }
        - [ ] ```
       
        - [ ] ### Processing Steps
        - [ ] 1. Fetch images from Logo Mark Media API
        - [ ] 2. Validate URLs (HTTP 200 response)
        - [ ] 3. Download and re-upload to Shopify (privacy/caching)
        - [ ] 4. Order by sequence number
        - [ ] 5. Set first image as featured
       
        - [ ] ### Validation Checklist
        - [ ] - [ ] At least 1 image per product
        - [ ] - [ ] Image URL is valid HTTP(S)
        - [ ] - [ ] Image file is 1-50 MB
        - [ ] - [ ] Image format is JPG, PNG, or GIF
        - [ ] - [ ] Downloaded image does not contain watermark (warn if found)
       
        - [ ] ---
       
        - [ ] ## 5. Easify Option Mapping
       
        - [ ] ### Expected Input from Product Data 2.0 or Config API
        - [ ] **Awaiting Michael's clarification: Do variants come in v1.0 or v2.0?**
       
        - [ ] ### If Provided by Logo Mark
        - [ ] ```
        - [ ] {
        - [ ]   "options": [
        - [ ]       {
        - [ ]         "id": "OPT001",
        - [ ]           "name": "Size",
        - [ ]             "type": "parent",
        - [ ]               "values": [
        - [ ]                   { "value": "2x3", "surcharge": 0 },
        - [ ]                       { "value": "3x4", "surcharge": 3.50 }
        - [ ]                         ]
        - [ ]                         },
        - [ ]                         {
        - [ ]                           "id": "OPT002",
        - [ ]                             "name": "Back Engraving",
        - [ ]                               "type": "dependent",
        - [ ]                                 "parentId": "OPT001",
        - [ ]                                   "visibleWhen": { "parentValue": "2x3" },
        - [ ]                                     "values": [
        - [ ]                                         { "value": "None", "surcharge": 0 },
        - [ ]                                             { "value": "2x3 Area", "surcharge": 0.80 }
        - [ ]                                               ]
        - [ ]                                               }
        - [ ]                                             ]
        - [ ]                                         }
        - [ ]                                     ```
       
        - [ ]                                 ### PPO Output: Easify Option Set (via API)
        - [ ]                             ```json
        - [ ]                         {
        - [ ]                       "name": "PKACBB Options",
        - [ ]                     "options": [
        - [ ]                     {
        - [ ]                       "name": "Size",
        - [ ]                         "type": "parent",
        - [ ]                           "values": ["2x3", "3x4"],
        - [ ]                             "modifiers": [
        - [ ]                                 { "value": "2x3", "price_modifier": 0 },
        - [ ]                                     { "value": "3x4", "price_modifier": 3.50 }
        - [ ]                                       ]
        - [ ]                                       },
        - [ ]                                       {
        - [ ]                                         "name": "Back Engraving",
        - [ ]                                           "type": "dependent",
        - [ ]                                             "parent_value": "2x3",
        - [ ]                                               "values": ["None", "2x3 Area"],
        - [ ]                                                 "modifiers": [
        - [ ]                                                     { "value": "None", "price_modifier": 0 },
        - [ ]                                                         { "value": "2x3 Area", "price_modifier": 0.80 }
        - [ ]                                                           ]
        - [ ]                                                           }
        - [ ]                                                         ]
        - [ ]                                                     }
        - [ ]                                                 ```
       
        - [ ]                                             ### If NOT Provided by Logo Mark
        - [ ]                                         Use category-based templates (to be created after Phase 1 analysis):
        - [ ]                                     - [ ] Belt Buckles template
        - [ ]                                 - [ ] [Other categories TBD]
       
        - [ ]                             ### Validation Checklist
        - [ ]                         - [ ] Parent option appears before dependent options
        - [ ]                     - [ ] Dependent option references valid parent value
        - [ ]                 - [ ] All option values have surcharge >= 0
        - [ ]             - [ ] Max 5 dependent options per parent (Easify limit)
        - [ ]         - [ ] Option names are unique within product
       
        - [ ]     ---
       
        - [ ] ## 6. Description Enhancement (AI Pipeline)
       
        - [ ] ### Input
        - [ ] ```
        - [ ] Logo Mark Description: "Belt Buckles with engraving"
        - [ ] ```
       
        - [ ] ### Transformation Process
        - [ ] ```
        - [ ] Step 1: Add context keywords
        - [ ]   → "Belt Buckles, custom, engraving, personalized"
       
        - [ ]   Step 2: AI Enhancement (Claude API)
        - [ ]     Prompt: "Enhance this description for contemporary brand voice,
        - [ ]            2-3 sentences, professional & energetic"
       
        - [ ]          Step 3: AI Output
        - [ ]        → "Premium custom belt buckles featuring full-color engraving.
        - [ ]         Make a bold personal statement with buckles that reflect your
        - [ ]          unique style. Perfect for corporate gifts or everyday wear."
       
        - [ ]      Step 4: Validation
        - [ ]    - Length check (2-3 sentences) ✓
        - [ ]      - Brand voice assessment (manual review) ✓
        - [ ]    - Profanity/brand safety check ✓
        - [ ]    ```
       
        - [ ]    ### Implementation Details
        - [ ]    ```javascript
        - [ ]    async function enhanceDescription(logomarkDescription, productCategory) {
        - [ ]      // Step 1: Add context
        - [ ]    const contextKeywords = getCategoryKeywords(productCategory);
        - [ ]      const withContext = `${logomarkDescription}. Keywords: ${contextKeywords}`;
       
        - [ ]      // Step 2: Call Claude API
        - [ ]    const enhancedDescription = await callClaudeAPI({
        - [ ]        prompt: `Enhance this product description for PPO brand voice
        - [ ]                 (contemporary, professional, energetic).
        - [ ]                          Keep to 2-3 sentences.
        - [ ]                                   Input: ${withContext}`,
        - [ ]                                   maxTokens: 150
        - [ ]                                 });
       
        - [ ]                                 // Step 3: Validate output
        - [ ]                               validateDescription(enhancedDescription);
       
        - [ ]                               return enhancedDescription;
        - [ ]                           }
        - [ ]                       ```
       
        - [ ]                   ### Validation Checklist
        - [ ]               - [ ] Description is 2-3 sentences (not more)
        - [ ]           - [ ] No profanity or brand safety issues
        - [ ]       - [ ] Tone matches PPO voice (test against style guide)
        - [ ]   - [ ] No placeholder text ([TBD], etc.)
        - [ ]   - [ ] Mentions key product benefits
       
        - [ ]   ---
       
        - [ ]   ## 7. Metafield Storage
       
        - [ ]   ### Purpose
        - [ ]   Store Logo Mark references in Shopify metafields for tracking, audits, and future updates.
       
        - [ ]   ### Fields to Store
        - [ ]   ```json
        - [ ]   {
        - [ ]     "namespace": "logomark",
        - [ ]   "metafields": [
        - [ ]       {
        - [ ]         "key": "product_id",
        - [ ]           "value": "LM-12345",
        - [ ]             "type": "string"
        - [ ]             },
        - [ ]             {
        - [ ]               "key": "sku_original",
        - [ ]                 "value": "PKACBB",
        - [ ]                   "type": "string"
        - [ ]                   },
        - [ ]                   {
        - [ ]                     "key": "moq",
        - [ ]                       "value": "50",
        - [ ]                         "type": "number_integer"
        - [ ]                         },
        - [ ]                         {
        - [ ]                           "key": "api_version",
        - [ ]                             "value": "Product_Data_2.0",
        - [ ]                               "type": "string"
        - [ ]                               },
        - [ ]                               {
        - [ ]                                 "key": "last_synced",
        - [ ]                                   "value": "2025-01-02T15:30:00Z",
        - [ ]                                     "type": "date_time"
        - [ ]                                     },
        - [ ]                                     {
        - [ ]                                       "key": "description_enhanced",
        - [ ]                                         "value": "true",
        - [ ]                                           "type": "boolean"
        - [ ]                                           }
        - [ ]                                         ]
        - [ ]                                     }
        - [ ]                                 ```
       
        - [ ]                             ---
       
        - [ ]                         ## 8. Validation & Error Handling
       
        - [ ]                     ### Pre-Import Checks
        - [ ]                 ```javascript
        - [ ]             const validationChecks = [
        - [ ]           {
        - [ ]           field: 'SKU',
        - [ ]           rule: 'alphanumeric, 1-20 chars',
        - [ ]           action: 'FAIL_IMPORT'
        - [ ]         },
        - [ ]       {
        - [ ]       field: 'Price',
        - [ ]       rule: 'positive number, <= $500',
        - [ ]       action: 'FAIL_IMPORT'
        - [ ]     },
        - [ ]   {
        - [ ]       field: 'Description',
        - [ ]       rule: 'max 500 chars, no HTML',
        - [ ]       action: 'WARN_LOG'
        - [ ]     },
        - [ ]   {
        - [ ]       field: 'Image URL',
        - [ ]       rule: 'valid HTTP(S), reachable',
        - [ ]       action: 'SKIP_IMAGE'
        - [ ]     },
        - [ ]   {
        - [ ]       field: 'MOQ',
        - [ ]       rule: 'integer >= 10',
        - [ ]       action: 'FAIL_IMPORT'
        - [ ]     }
        - [ ] ];
        - [ ] ```
       
        - [ ] ### Error Handling Strategy
        - [ ] | Error Type | Action | Retry | Log |
        - [ ] |-----------|--------|-------|-----|
        - [ ] | Invalid SKU | Fail import | No | ERROR |
        - [ ] | Network timeout | Skip product | Yes (3x) | WARN |
        - [ ] | Image 404 | Use placeholder | No | WARN |
        - [ ] | Invalid price | Fail import | No | ERROR |
        - [ ] | API auth failure | Halt all | No | ERROR |
       
        - [ ] ---
       
        - [ ] ## 9. Data Mapping Workflow
       
        - [ ] ```
        - [ ] ┌──────────────────────────────┐
        - [ ] │  Logo Mark SOAP API Call     │
        - [ ] └────────────┬─────────────────┘
        - [ ]              │
        - [ ]             ┌─────────▼─────────┐
        - [ ]            │ Parse XML Response│
        - [ ]           └─────────┬─────────┘
        - [ ]                    │
        - [ ]                   ┌─────────▼─────────────────────────┐
        - [ ]                  │ Apply Transformations:            │
        - [ ]                 │ - SKU shift                       │
        - [ ]                │ - Price calculation               │
        - [ ]               │ - Easify mapping                  │
        - [ ]              │ - Description enhancement         │
        - [ ]             └─────────┬─────────────────────────┘
        - [ ]                      │
        - [ ]                     ┌─────────▼──────────────────┐
        - [ ]                    │ Validation Checks:         │
        - [ ]                   │ - Pre-import validation    │
        - [ ]                  │ - Format consistency       │
        - [ ]                 │ - Required fields present  │
        - [ ]                └─────────┬──────────────────┘
        - [ ]                         │
        - [ ]                           ┌──────┴──────┐
        - [ ]                             │             │
        - [ ]                            PASS         FAIL
        - [ ]                              │             │
        - [ ]                                ▼             ▼
        - [ ]                               Shopify      Error Log
        - [ ]                              Create       (Skip Product)
        - [ ]                          ```
       
        - [ ]                      ---
       
        - [ ]                  ## 10. Next Steps
       
        - [ ]              1. **Receive Logo Mark Field Specification** (Excel file from Michael - end of week)
        - [ ]          2. **Complete Product Data Mapping** (Section 2) with actual field names
        - [ ]      3. **Clarify Variant/Option Handling** - Does Product Pricing & Config 1.0 include option data?
        - [ ]  4. **Finalize Image Handling** - How many images per product? Sizes?
        - [ ]  5. **Test Mappings** with sample Logo Mark data
        - [ ]  6. **Implement Validation** for each transformation rule
        - [ ]  
