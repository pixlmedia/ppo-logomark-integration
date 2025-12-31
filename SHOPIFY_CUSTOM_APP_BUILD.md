# Custom Shopify App Build Guide - Logo Mark Pricing

## Overview
Building a custom Shopify app (internal/private) on the password-protected PPO live site to handle dynamic pricing for Logo Mark products.

## Architecture

### Core Components
1. **Pricing Engine** - Calculates base price + surcharges + setup charges
2. 2. **Metafield Reader** - Reads logomark_custom.* fields from products
   3. 3. **Frontend** - Liquid template + JavaScript for product page display
      4. 4. **Data Flow** - PromoStandards API → Metafields → Pricing Engine → Product Page
        
         5. ## Step 1: Create Custom App in Shopify Admin
        
         6. Navigate to: **Apps and integrations → Develop apps**
         7. - Click "Create an app"
            - - Name: "Logo Mark Dynamic Pricing"
              - - Configuration → Admin API scopes needed:
                -   - `read_products` - Read product metafields
                    -   - `write_products` - Update product metafields (for imports)
                        -   - `read_order` - (optional, for order analysis)
                         
                            - Save and get your Admin API credentials (access token, API key)
                         
                            - ## Step 2: Pricing Engine Logic
                         
                            - The app must calculate:
                         
                            - ```javascript
                              function calculatePrice(product, selectedOptions, quantity) {
                                // Get metafield data
                                const quantityBreaks = product.metafields.find(m => m.key === 'quantity_breaks').value;
                                const options = product.metafields.find(m => m.key === 'options').value;

                                // 1. Find quantity tier
                                const tier = quantityBreaks.find(t =>
                                  quantity >= t.min_qty && (!t.max_qty || quantity <= t.max_qty)
                                );

                                // 2. Calculate per-unit surcharges from selected options
                                let perUnitSurcharge = 0;
                                let totalSetupCharge = 0;

                                selectedOptions.forEach(optionId => {
                                  const option = options.find(o => o.id === optionId);
                                  perUnitSurcharge += option.per_unit_surcharge || 0;
                                  totalSetupCharge += option.setup_charge || 0;
                                });

                                // 3. Calculate total
                                const unitPrice = tier.base_price + perUnitSurcharge;
                                const subtotal = unitPrice * quantity;
                                const total = subtotal + totalSetupCharge;

                                return {
                                  unitPrice,
                                  subtotal,
                                  setupCharge: totalSetupCharge,
                                  total,
                                  breakdown: {
                                    base: tier.base_price,
                                    surcharges: perUnitSurcharge,
                                    setup: totalSetupCharge
                                  }
                                };
                              }
                              ```

                              ## Step 3: Liquid Template Integration

                              In product.liquid or a custom section:

                              ```liquid
                              {%  if product.metafields.custom.quantity_breaks %}
                                <div id="dynamic-pricing-table">
                                  <!-- Pricing will render here via JavaScript -->
                                </div>

                                <script>
                                  // Load metafield data
                                  const pricingData = {
                                    quantityBreaks: {{ product.metafields.custom.quantity_breaks | json }},
                                    options: {{ product.metafields.custom.options | json }}
                                  };

                                  // Call pricing engine on quantity/option changes
                                  function updatePrice() {
                                    const qty = document.querySelector('[name="quantity"]').value;
                                    const selectedOptions = getSelectedOptions();
                                    const price = calculatePrice(pricingData, selectedOptions, qty);
                                    displayPrice(price);
                                  }
                                </script>
                              {% endif %}
                              ```

                              ## Step 4: Implementation Approach

                              ### Option A: Custom App with Frontend (Recommended)
                              - Create Node.js/JavaScript app hosted on your infrastructure
                              - - Use Shopify Admin REST API to read metafields
                                - - Deploy price display via Liquid theme code
                                  - - Runs on PPO's password-protected site first
                                   
                                    - ### Option B: Pure Liquid/Theme Approach (Simpler)
                                    - - No external app server needed
                                      - - Use Liquid directly in theme to read metafields
                                        - - JavaScript calculations on product page
                                          - - Better for password-protected site testing
                                           
                                            - ## Step 5: Testing Strategy
                                           
                                            - 1. **Manual Testing** on PPO password-protected site:
                                              2.    - Select different quantities
                                                    -    - Select/deselect options
                                                         -    - Verify price calculations match expected results
                                                              -    - Test all quantity breaks
                                                               
                                                                   - 2. **Expected Results** (from earlier analysis):
                                                                     3.    - Base qty pricing from quantity_breaks metafield
                                                                           -    - Per-unit color surcharges add correctly
                                                                                -    - One-time setup charges add correctly
                                                                                     -    - Different options trigger correct pricing tiers
                                                                                      
                                                                                          - ## Step 6: Deployment to Production
                                                                                      
                                                                                          - When ready to go live (DNS configured):
                                                                                          - 1. Same app works on live domain
                                                                                            2. 2. No changes needed - just DNS switch
                                                                                               3. 3. Custom app continues reading metafields
                                                                                                  4. 4. Pricing displays automatically
                                                                                                    
                                                                                                     5. ## Data Structure Reference
                                                                                                    
                                                                                                     6. ### quantity_breaks JSON
                                                                                                     7. ```json
                                                                                                        [
                                                                                                          {"min_qty": 150, "max_qty": 249, "base_price": 4.89},
                                                                                                          {"min_qty": 250, "max_qty": 499, "base_price": 4.59},
                                                                                                          {"min_qty": 500, "base_price": 4.29}
                                                                                                        ]
                                                                                                        ```
                                                                                                        
                                                                                                        ### options JSON
                                                                                                        ```json
                                                                                                        [
                                                                                                          {
                                                                                                            "id": "option_colors",
                                                                                                            "name": "Additional Imprint Colors",
                                                                                                            "type": "multiselect",
                                                                                                            "per_unit_surcharge": 0.10,
                                                                                                            "setup_charge": 0
                                                                                                          },
                                                                                                          {
                                                                                                            "id": "option_setup",
                                                                                                            "name": "Setup Charge",
                                                                                                            "type": "select",
                                                                                                            "per_unit_surcharge": 0,
                                                                                                            "setup_charge": 50
                                                                                                          }
                                                                                                        ]
                                                                                                        ```
                                                                                                        
                                                                                                        ## Next Steps
                                                                                                        
                                                                                                        1. **Create the custom app** in Shopify Admin
                                                                                                        2. 2. **Choose implementation** (Option A or B above)
                                                                                                           3. 3. **Build the pricing engine** using the logic provided
                                                                                                              4. 4. **Integrate into theme** with Liquid template
                                                                                                                 5. 5. **Test on password-protected site**
                                                                                                                    6. 6. **Iterate based on findings**
                                                                                                                      
                                                                                                                       7. ## CSS Consistency
                                                                                                                      
                                                                                                                       8. Remember to extract and apply the exact CSS from existing Tablepress pricing tables to ensure visual consistency across all Logo Mark products. This will be crucial when moving from password-protected to live.
                                                                                                                      
                                                                                                                       9. ---
                                                                                                                      
                                                                                                                       10. **Phase 4 Status**: Custom App Build Guide Created
                                                                                                                       11. **Next**: Build and integrate custom pricing app on PPO password-protected site  


## CRITICAL: Real-Time Inventory Sync Required

**Option A is MANDATORY** (not optional). After 8 years discontinuing Logo Mark due to inventory failures (customers upset over out-of-stock orders after proofs), inventory must be real-time and accurate.

### Per-Color Inventory Control

Logo Mark provides qty-on-hand per color. Store in metafield: `logomark_custom.inventory_status` (JSON)

**Structure**:
```json
{
  "last_updated": "2025-12-31T14:30:00Z",
  "low_stock_threshold": 5,
  "colors": {
    "red": {"qty_on_hand": 45, "status": "in_stock"},
    "blue": {"qty_on_hand": 2, "status": "low_stock"},
    "green": {"qty_on_hand": 0, "status": "out_of_stock"},
    "yellow": {"qty_on_hand": 120, "status": "in_stock"}
  }
}
```

### Product Behavior
- **IN STOCK**: Option enabled, can select normally
- - **LOW STOCK** (below threshold): Option enabled with warning badge "Only X left"
  - - **OUT OF STOCK**: Option disabled/grayed, cannot select
    - - **Key**: Disable only out-of-stock colors. Product stays available if ANY color in stock.
     
      - ### Custom App Requirements
      - 1. Pull inventory from Logo Mark API/feed (hourly sync)
        2. 2. Update `inventory_status` metafield per product
           3. 3. Modify product.liquid to disable out-of-stock color options
              4. 4. Add stock status labels to dropdown options
                 5. 5. Prevent checkout if all colors out of stock
                   
                    6. Phase 4 Status: Custom App Build Guide + Inventory Architecture Defined
                    7. Next Phase: Build Node.js app for inventory sync + integrate with Shopify
                    8. 
