# Shopify Pricing Architecture for Logo Mark Products

## Executive Summary

**Architectural Decision**: Use **Shopify Metafields + Custom Liquid Template** for Logo Mark dynamic pricing instead of Tablepress.

**Rationale**: Logo Mark pricing data is dynamic and changes frequently. It requires API integration and real-time syncing. Tablepress is designed for static, manually-managed pricing tables. Custom architecture enables:
- Fully automated price sync from Logo Mark APIs
- - Scalable to 100+ products
  - - Seamless UI consistency with existing pricing tables
    - - Direct integration with Easify option pricing
     
      - ---

      ## Problem Statement

      ### Why Tablepress Alone Won't Work

      Tablepress Size Chart app creates **static HTML tables** that are:
      - Manually maintained through the Shopify admin
      - - Not designed for API-driven data
        - - Not suitable for frequent price updates
          - - Not scalable to bulk product imports
           
            - Logo Mark integration requires:
            - - **Dynamic pricing** from Logo Mark APIs (Product Pricing & Config 1.0)
              - - **Quantity break variations** (10, 25, 50, 100, 250, 500+)
                - - **Option surcharges** (size, engraving area, etc. from Easify)
                  - - **Regular syncing** of pricing data
                    - - **Bulk product support** (100+ products)
                     
                      - **Conclusion**: Manual Tablepress entry is not viable. We need automated, dynamic pricing.
                     
                      - ---

                      ## Proposed Solution Architecture

                      ### Overview

                      ```
                      Logo Mark SOAP API (Pricing Data)
                              ↓
                      SOAP Client (Node.js/PHP)
                              ↓
                      Parse & Transform
                              ↓
                      Store in Shopify Metafields (JSON)
                              ↓
                      Custom Liquid Template + JavaScript
                              ↓
                      Rendered Pricing Table on Storefront
                      ```

                      ### Component 1: Metafield Structure

                      Store Logo Mark pricing as structured JSON in Shopify metafields:

                      ```json
                      Namespace: logomark_pricing

                      Keys:
                        - quantity_breaks
                          Type: JSON
                          Value: [10, 25, 50, 100, 250, 500]

                        - base_prices
                          Type: JSON
                          Value: [5.00, 4.75, 4.50, 4.25, 4.00, 3.75]
                          (prices corresponding to each quantity break)

                        - option_surcharges
                          Type: JSON
                          Value: {
                            "size": {
                              "2x3": 0.00,
                              "3x4": 3.50,
                              "5x7": 7.00
                            },
                            "engraving": {
                              "none": 0.00,
                              "back": 0.80
                            }
                          }

                        - moq (Minimum Order Quantity)
                          Type: number
                          Value: 50

                        - last_synced
                          Type: datetime
                          Value: 2025-01-02T15:30:00Z
                      ```

                      ### Component 2: Data Population (Phase 1)

                      During product import (SOAP client script):

                      1. **Fetch** pricing from Logo Mark Product Pricing & Config 1.0 API
                      2. 2. **Transform** quantity breaks and base prices
                         3. 3. **Store** in metafields above
                            4. 4. **Include** with product creation request to Shopify REST API
                              
                               5. **Code Pattern**:
                               6. ```javascript
                                  const productData = {
                                    title: "Custom Belt Buckles",
                                    sku: "PPDBCC",
                                    metafields: [
                                      {
                                        namespace: "logomark_pricing",
                                        key: "quantity_breaks",
                                        value: JSON.stringify([10, 25, 50, 100]),
                                        type: "json"
                                      },
                                      {
                                        namespace: "logomark_pricing",
                                        key: "base_prices",
                                        value: JSON.stringify([5.00, 4.50, 4.25, 4.00]),
                                        type: "json"
                                      },
                                      // ... more metafields
                                    ]
                                  };

                                  // Create product with Shopify REST API
                                  ```

                                  ---

                                  ## Component 3: UI Rendering (Phase 3)

                                  ### Shopify Theme Extension (Liquid + JavaScript)

                                  Create a custom Liquid snippet that:

                                  1. **Fetches** metafield data for the product
                                  2. 2. **Combines** base prices with Easify option surcharges
                                     3. 3. **Renders** an HTML pricing table
                                        4. 4. **Applies** CSS matching existing Tablepress styling
                                           5. 5. **Handles** Easify option selection updates
                                             
                                              6. **Liquid Template Structure**:
                                              7. ```liquid
                                                 {% if product.metafields.logomark_pricing %}
                                                   <div class="logomark-pricing-table">
                                                     <table class="pricing-table">
                                                       <thead>
                                                         <tr>
                                                           <th>Quantity</th>
                                                           {% for size in product.metafields.logomark_pricing.option_surcharges.size %}
                                                             <th>{{ size[0] }}</th>
                                                           {% endfor %}
                                                         </tr>
                                                       </thead>
                                                       <tbody>
                                                         {% for qty in product.metafields.logomark_pricing.quantity_breaks %}
                                                           <tr>
                                                             <td>{{ qty }}+</td>
                                                             {% for size in options %}
                                                               <td>${{ base_price_at(qty) + surcharge_for(size) }}</td>
                                                             {% endfor %}
                                                           </tr>
                                                         {% endfor %}
                                                       </tbody>
                                                     </table>
                                                   </div>
                                                 {% endif %}
                                                 ```

                                                 ### CSS Consistency (Critical Requirement)

                                                 **Extract existing Tablepress CSS**:
                                                 1. Inspect Tablepress-rendered tables on live PPO store
                                                 2. 2. Copy all styling rules for:
                                                    3.    - Table borders, spacing, alignment
                                                          -    - Cell padding and font styling
                                                               -    - Hover states and alternate row colors
                                                                    -    - Responsive behavior on mobile
                                                                         -    - Color scheme and brand consistency
                                                                          
                                                                              - **Apply to custom Liquid output**:
                                                                              - ```css
                                                                                .logomark-pricing-table {
                                                                                  /* All Tablepress CSS rules */
                                                                                }

                                                                                .logomark-pricing-table table {
                                                                                  /* Match Tablepress table.pricing-table */
                                                                                }
                                                                                ```

                                                                                **Result**: Indistinguishable UI between Tablepress and Logo Mark pricing tables.

                                                                                ---

                                                                                ## Integration with Easify Options

                                                                                ### How It Works

                                                                                When a customer selects an Easify option:

                                                                                1. JavaScript listener detects option change
                                                                                2. 2. Looks up surcharge for selected option
                                                                                3. Recalculates all prices in table
                                                                                4. 4. Updates display in real-time
                                                                                  
                                                                                   5. **JavaScript Pattern**:
                                                                                   6. ```javascript
                                                                                      // Listen for Easify option changes
                                                                                      document.addEventListener('easify:optionChanged', function(event) {
                                                                                        const selectedSize = event.detail.option;
                                                                                        const surcharge = pricingData.option_surcharges.size[selectedSize];

                                                                                        // Recalculate and update pricing table
                                                                                        updateTableWithSurcharge(surcharge);
                                                                                      });
                                                                                      ```

                                                                                      ### Tablepress Price Interaction

                                                                                      **For other suppliers** (non-Logo Mark):
                                                                                      - Continue using Tablepress Size Chart app
                                                                                      - - No changes needed
                                                                                        - - Works alongside new Logo Mark pricing system
                                                                                         
                                                                                          - **For Logo Mark products**:
                                                                                          - - Use custom Liquid template (not Tablepress)
                                                                                            - - Dynamic pricing updates via metafields
                                                                                              - - Direct Easify integration
                                                                                               
                                                                                                - ---

                                                                                                ## Implementation Timeline

                                                                                                ### Phase 1: Foundation (Weeks 1-2)
                                                                                                - ✅ Document this architecture
                                                                                                - - Include metafield setup in SOAP client (DATA_MAPPING.md)
                                                                                                  - - Design metafield JSON structure
                                                                                                    - - Plan API payload format
                                                                                                     
                                                                                                      - ### Phase 2: Pricing & Options (Weeks 3-4)
                                                                                                      - - Implement metafield population in product import script
                                                                                                        - - Test metafield data storage in Shopify
                                                                                                          - - No Liquid template yet (prepared for Phase 3)
                                                                                                           
                                                                                                            - ### Phase 3: Media & Descriptions (Weeks 5-6)
                                                                                                            - - ✅ Extract Tablepress CSS from live PPO site
                                                                                                              - - Create custom Liquid snippet with CSS matching
                                                                                                                - - Implement JavaScript option listener
                                                                                                                  - - Test pricing table rendering
                                                                                                                    - - Test Easify option interaction
                                                                                                                     
                                                                                                                      - ### Phase 5: Testing & Launch (Weeks 9-10)
                                                                                                                      - - Full end-to-end testing of pricing system
                                                                                                                        - - Validate CSS consistency across browsers
                                                                                                                          - - Performance testing (large pricing tables)
                                                                                                                            - - Go-live with first batch of Logo Mark products
                                                                                                                             
                                                                                                                              - ---
                                                                                                                              
                                                                                                                              ## Technical Details
                                                                                                                              
                                                                                                                              ### Metafield API Example
                                                                                                                              
                                                                                                                              **Create product with pricing metafields**:
                                                                                                                              ```
                                                                                                                              POST /admin/api/2024-01/products.json

                                                                                                                              {
                                                                                                                                "product": {
                                                                                                                                  "title": "Custom Belt Buckles",
                                                                                                                                  "vendor": "Logo Mark",
                                                                                                                                  "metafields": [
                                                                                                                                        {
                                                                                                                                      "namespace": "logomark_pricing",
                                                                                                                                      "key": "quantity_breaks",
                                                                                                                                      "type": "json",
                                                                                                                                      "value": "[10,25,50,100,250,500]"
                                                                                                                                    },
                                                                                                                                    {
                                                                                                                                      "namespace": "logomark_pricing",
                                                                                                                                      "key": "base_prices",
                                                                                                                                      "type": "json",
                                                                                                                                      "value": "[5.00,4.75,4.50,4.25,4.00,3.75]"
                                                                                                                                    },
                                                                                                                                    {
                                                                                                                                      "namespace": "logomark_pricing",
                                                                                                                                      "key": "option_surcharges",
                                                                                                                                      "type": "json",
                                                                                                                                      "value": "{\"size\":{\"2x3\":0,\"3x4\":3.50},\"engraving\":{\"none\":0,\"back\":0.80}}"
                                                                                                                                    },
                                                                                                                                    {
                                                                                                                                      "namespace": "logomark_pricing",
                                                                                                                                      "key": "moq",
                                                                                                                                      "type": "number_integer",
                                                                                                                                      "value": "50"
                                                                                                                                    }
                                                                                                                                  ]
                                                                                                                                }
                                                                                                                              }
                                                                                                                              ```
                                                                                                                              
                                                                                                                              ### Liquid Retrieval Pattern
                                                                                                                              
                                                                                                                              ```liquid
                                                                                                                              {% assign moq = product.metafields.logomark_pricing.moq.value %}
                                                                                                                              {% assign qtys = product.metafields.logomark_pricing.quantity_breaks.value %}
                                                                                                                              {% assign prices = product.metafields.logomark_pricing.base_prices.value %}
                                                                                                                              {% assign surcharges = product.metafields.logomark_pricing.option_surcharges.value %}

                                                                                                                              <!-- Now render table using these variables -->
                                                                                                                              ```
                                                                                                                              
                                                                                                                              ---
                                                                                                                              
                                                                                                                              ## Advantages of This Approach
                                                                                                                              
                                                                                                                              ✅ **Fully Automated**: Pricing syncs whenever products are re-imported
                                                                                                                              ✅ **Scalable**: Handles 100+ products without manual effort
                                                                                                                              ✅ **Flexible**: Easy to update pricing logic without modifying code
                                                                                                                              ✅ **Maintainable**: Centralized pricing source (Logo Mark API)
                                                                                                                              ✅ **User Transparent**: Customers see seamless pricing tables
                                                                                                                              ✅ **CSS Consistent**: Matches existing Tablepress styling
                                                                                                                              ✅ **Easify Integrated**: Option surcharges calculate in real-time
                                                                                                                              ✅ **Future-Proof**: Ready for other suppliers' dynamic pricing
                                                                                                                              
                                                                                                                              ---
                                                                                                                              
                                                                                                                              ## Known Constraints
                                                                                                                              
                                                                                                                              - Requires custom Liquid template (not a built-in app)
                                                                                                                              - - JavaScript needed for real-time option price updates
                                                                                                                                - - Need to extract and maintain CSS from Tablepress
                                                                                                                                  - - Metafield size limits (Shopify has 10MB per product limit for all metafields combined)
                                                                                                                                   
                                                                                                                                    - ---
                                                                                                                                    
                                                                                                                                    ## Next Steps
                                                                                                                                    
                                                                                                                                    1. ✅ Approve this architecture
                                                                                                                                    2. 2. Design metafield JSON structure (with Michael's field spec)
                                                                                                                                       3. 3. Update DATA_MAPPING.md with metafield field mapping
                                                                                                                                          4. 4. Phase 1: Implement metafield storage in SOAP client
                                                                                                                                             5. 5. Phase 3: Create Liquid template and CSS extraction
                                                                                                                                                6. 6. Phase 5: Test and launch with first batch of products
                                                                                                                                                  
                                                                                                                                                   7. ---
                                                                                                                                                  
                                                                                                                                                   8. ## Questions & Decisions
                                                                                                                                                  
                                                                                                                                                   9. **Q**: What if Logo Mark pricing changes mid-day?
                                                                                                                                                   10. **A**: Re-run the product import script to refresh metafields. Can be scheduled daily via GitHub Actions.
                                                                                                                                                  
                                                                                                                                                   11. **Q**: What about Tablepress products from other suppliers?
                                                                                                                                                   12. **A**: Tablepress app continues working unchanged. Only Logo Mark products use the custom system.
                                                                                                                                                  
                                                                                                                                                   13. **Q**: Can we version the metafield format?
                                                                                                                                                   14. **A**: Yes. Include a `schema_version` key in metafields if we need to evolve the structure later.
                                                                                                                                                  
                                                                                                                                                   15. **Q**: Performance impact of dynamic pricing table?
                                                                                                                                                   16. **A**: Minimal. Table rendered on page load, JavaScript handles option updates. No API calls needed on storefront.
                                                                                                                                                  
                                                                                                                                                   17. ---
                                                                                                                                                  
                                                                                                                                                   18. **Status**: Ready for approval and integration into Phase 1-3 planning
                                                                                                                                                   19. **Last Updated**: 12/30/2025
                                                                                                                                                   20. **Approved By**: (pending)
