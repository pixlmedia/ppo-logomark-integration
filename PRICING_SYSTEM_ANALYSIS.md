# Pricing System Analysis: Three Real-World Product Patterns

## Executive Summary

We analyzed three real products across two platforms (PK Shopify with Easify, LogoMark) to understand pricing complexity and inform our custom Shopify app architecture. Key finding: **Setup charges (one-time fees) are critical and must be captured in our pricing logic.**

---

## Pattern 1: Complex - PK Die Cast Dog Tag

**Product:** Brushed Antiqued Custom Designed Dog Tags  
**Platform:** PromotionalKeychains.biz (Shopify + Easify)  
**Complexity Level:** HIGH

### Pricing Structure

**Base Quantity-Break Table:**
| Quantity | One Side | Two Sides |
|----------|----------|-----------|
| 150      | $3.48    | $3.63     |
| 250      | $3.29    | $3.44     |
| 500      | $3.11    | $3.26     |
| 1000     | $3.07    | $3.22     |
| 2500     | $3.04    | $3.19     |

### Key Components

1. **Per-Unit Linear Surcharge (Two-Sided Option)**
2.    - When customer selects "I want a two sided item"
      -    - Adds +$0.15 per unit to the base quantity-break price
           -    - Built into the pricing table as a separate column
            
                - 2. **Per-Unit Color Surcharge**
                  3.    - "I would like 1 color added (.05) [+$0.05 USD]"
                        -    - Adds +$0.05 per unit for each additional color
                             -    - Stacks with other surcharges
                              
                                  - 3. **One-Time Setup Charge (CRITICAL)**
                                    4.    - When two-sided mold is selected: "Mold for front and back side (one time $120) [+$120.00 USD]"
                                          -    - **This is a one-time charge, NOT per unit**
                                               -    - Triggered by specific option selections (two-sided)
                                                    -    - Must be captured separately
                                                     
                                                         - ### Pricing Calculation Example
                                                     
                                                         - **Scenario:** 500 units, two-sided, 1 additional color, keyring
                                                     
                                                         - ```
                                                           Base price (500 qty, two-sided): 500 × $3.26 = $1,630
                                                           (Note: $3.26 already includes the +$0.15 two-sided per-unit cost)

                                                           Additional color surcharge: 500 × $0.05 = $25

                                                           One-time mold setup: $120 (added once, not multiplied)

                                                           Displayed total: $1,630 + $25 + $120 = $1,775
                                                           ```

                                                           ### Key Insight

                                                           **The pricing table already accounts for per-unit costs of two-sided customization**, but the one-time mold charge is shown separately as a distinct field. The Easify option dropdown shows: `[+$0.15 USD]` for the per-unit cost AND `[+$120.00 USD]` for the one-time setup.

                                                           ---

                                                           ## Pattern 2: Simple - PK Vinyl Keychain

                                                           **Product:** Classic Dump Truck Vinyl Keychain PK3978
                                                           **Platform:** PromotionalKeychains.biz (Shopify + Easify)
                                                           **Complexity Level:** LOW

                                                           ### Pricing Structure

                                                           **Clean Color-Based Table:**
                                                           | Quantity | 1 Color | 2 Colors | 3 Colors |
                                                           |----------|---------|----------|----------|
                                                           | 250      | $1.27   | $1.40    | $1.53    |
                                                           | 500      | $1.05   | $1.18    | $1.31    |
                                                           | 1000     | $1.00   | $1.12    | $1.24    |
                                                           | 2500     | $0.97   | $1.08    | $1.19    |
                                                           | 5000     | $0.92   | $1.02    | $1.12    |
                                                           | 10000    | $0.90   | $1.00    | $1.10    |

                                                           ### Key Components

                                                           1. **Per-Unit Color Surcharges**
                                                           2.    - "2 Imprints (2 colors front) ($0.13 USD)" = +$0.13 per unit
                                                                 -    - "3 Imprints (3 colors front) ($0.26 USD)" = +$0.26 per unit
                                                                      -    - Linear, transparent, straightforward
                                                                       
                                                                           - 2. **Optional Imprint Setup Field**
                                                                             3.    - Appears as "Imprint Setup" dropdown after color selection
                                                                                   -    - Likely triggers a setup charge (similar to dog tag mold charge)
                                                                                        -    - **Setup charges should be supported** even if not always used
                                                                                         
                                                                                             - ### Pricing Calculation Example
                                                                                         
                                                                                             - **Scenario:** 1000 units, 2 colors (2 imprints)
                                                                                         
                                                                                             - ```
                                                                                               Base price (1 color, 1000 qty): 1000 × $1.00 = $1,000
                                                                                               Additional color surcharge: 1000 × $0.13 = $130

                                                                                               Displayed total: $1,000 + $130 = $1,130
                                                                                               ```

                                                                                               ### Key Insight

                                                                                               **This is the "ideal" pricing model for simplicity.** Multiple imprint colors as a primary option drives per-unit surcharges. LogoMark products simplified to this pattern would be much easier to implement.

                                                                                               ---

                                                                                               ## Pattern 3: Location-Based - LogoMark Broome Notebook

                                                                                               **Product:** Broome Junior Notebook with Pen, Flags & Sticky Notes (KP2444B)
                                                                                               **Platform:** logomark.com
                                                                                               **Complexity Level:** MEDIUM

                                                                                               ### Pricing Structure

                                                                                               **Two-Tier Model:**
                                                                                               | Qty  | Decorated (c) | Blank (c) |
                                                                                               |------|---------------|----------|
                                                                                               | 150  | $4.89         | $4.40    |
                                                                                               | 250  | $4.59         | $4.13    |
                                                                                               | 500  | $4.29         | $3.86    |
                                                                                               | 1000 | $3.99         | $3.59    |

                                                                                               ### Available Decoration Locations & Constraints

                                                                                               | Location | Size | Max Colors |
                                                                                               |----------|------|-----------|
                                                                                               | Front Center | 1 7/8" W x 3" H | 1 |
                                                                                               | Front Top | 1 7/8" W x 3" H | 1 |
                                                                                               | Front Bottom | 1 7/8" W x 3" H | 1 |
                                                                                               | Backside Center | 2 1/2" W x 3" H | 1 |
                                                                                               | Right Read - Side Below Clip | 1 1/4" W x 1/4" H | 3 |

                                                                                               ### Key Components

                                                                                               1. **Decoration vs Non-Decoration Pricing**
                                                                                               2.    - "Decorated" vs "Blank" pricing differs significantly
                                                                                                     -    - Selection affects base price tier
                                                                                                      
                                                                                                          - 2. **Location-Based Options**
                                                                                                            3.    - Multiple locations available but not all used per product
                                                                                                                  -    - Each location has maximum color limits
                                                                                                                       -    - Some locations allow multiple colors (side clip: 3 colors max)
                                                                                                                        
                                                                                                                            - 3. **Setup Charges**
                                                                                                                              4.    - "SETUP UNDECORATED" button suggests separate setup pricing
                                                                                                                                    -    - "Additional Charges" button indicates extra fees for certain options
                                                                                                                                     
                                                                                                                                         - ### Key Insight
                                                                                                                                     
                                                                                                                                         - **LogoMark's approach is location-centric, not color-centric.** If we simplify LogoMark products to use only one primary location and focus on multiple imprint colors (like the vinyl keychain pattern), they become much simpler to manage.
                                                                                                                                     
                                                                                                                                         - ---
                                                                                                                                         
                                                                                                                                         ## Custom App Architecture Recommendation
                                                                                                                                         
                                                                                                                                         ### Recommended: Monolithic Application
                                                                                                                                         
                                                                                                                                         **Why One App Instead of Splitting?**
                                                                                                                                         
                                                                                                                                         1. **Single Source of Truth:** All pricing logic in one place
                                                                                                                                         2. 2. **Consistent Precedent:** Easify handles all pricing in one extension
                                                                                                                                            3. 3. **Simpler Data Structure:** Metafields don't need complex app coordination
                                                                                                                                               4. 4. **Easier Maintenance:** One set of business logic
                                                                                                                                                  5. 5. **Better User Experience:** Consistent pricing updates across the store
                                                                                                                                                    
                                                                                                                                                     6. **Internal Organization:** Even in a monolithic app, we separate concerns with distinct function groups (base pricing, surcharges, setup charges, dependencies).
                                                                                                                                                    
                                                                                                                                                     7. ---
                                                                                                                                                    
                                                                                                                                                     8. ## Required Features for the Custom App
                                                                                                                                                    
                                                                                                                                                     9. ### 1. Dynamic Quantity-Break Pricing
                                                                                                                                                     10. - Read quantity from customer input
                                                                                                                                                         - - Look up appropriate price tier from product metafields
                                                                                                                                                           - - Apply: `base_price × quantity`
                                                                                                                                                            
                                                                                                                                                             - ### 2. Per-Unit Option Surcharges (Core Feature)
                                                                                                                                                             - - Support multiple color options
                                                                                                                                                               - - Each option has a per-unit cost (e.g., +$0.05, +$0.13)
                                                                                                                                                                 - - **Calculation:** `(base_price + color_surcharge + other_surcharges) × quantity`
                                                                                                                                                                   - - **Handles:** Vinyl keychain pattern, simplified LogoMark pattern
                                                                                                                                                                    
                                                                                                                                                                     - ### 3. One-Time Setup Charges (CRITICAL)
                                                                                                                                                                     - - Some option selections trigger a **one-time charge** (not per unit)
                                                                                                                                                                       - - Examples:
                                                                                                                                                                         -   - Dog tag two-sided mold: +$120 (one time)
                                                                                                                                                                             -   - Reverse side customization setup
                                                                                                                                                                                 -   - Imprint method setup
                                                                                                                                                                                     - - **Calculation:** `Add setup_charge once to total, don't multiply by quantity`
                                                                                                                                                                                       - - **Implementation Options (see below)**
                                                                                                                                                                                        
                                                                                                                                                                                         - ### 4. Cascading/Dependent Options
                                                                                                                                                                                         - - Some options only appear based on previous selections
                                                                                                                                                                                           - - Example: "Imprint Setup" dropdown appears after color selection
                                                                                                                                                                                             - - Handle conditional visibility and pricing
                                                                                                                                                                                              
                                                                                                                                                                                               - ### 5. CSS Consistency
                                                                                                                                                                                               - - Extract exact CSS from existing Tablepress pricing tables
                                                                                                                                                                                                 - - Apply to custom app output
                                                                                                                                                                                                   - - Ensure seamless visual consistency across product pages
                                                                                                                                                                                                    
                                                                                                                                                                                                     - ---
                                                                                                                                                                                                     
                                                                                                                                                                                                     ## Setup Charge Implementation: Two Options
                                                                                                                                                                                                     
                                                                                                                                                                                                     ### Option A: Separate One-Time Charge Field (RECOMMENDED)
                                                                                                                                                                                                     
                                                                                                                                                                                                     **How it works:**
                                                                                                                                                                                                     - Create a metafield: `custom.setup_charge` (or specific field per option)
                                                                                                                                                                                                     - - When option is selected that triggers setup (e.g., "two-sided mold"), add the setup charge to the total
                                                                                                                                                                                                       - - Charge appears as a separate line item in pricing breakdown
                                                                                                                                                                                                        
                                                                                                                                                                                                         - **Metafield structure:**
                                                                                                                                                                                                         - ```json
                                                                                                                                                                                                           {
                                                                                                                                                                                                             "options": [
                                                                                                                                                                                                               {
                                                                                                                                                                                                                 "name": "Two Sided",
                                                                                                                                                                                                                 "value": "two-sided",
                                                                                                                                                                                                                 "per_unit_cost": 0.15,
                                                                                                                                                                                                                 "setup_charge": 120,
                                                                                                                                                                                                                 "setup_description": "Mold for front and back side (one time)"
                                                                                                                                                                                                               }
                                                                                                                                                                                                             ]
                                                                                                                                                                                                           }
                                                                                                                                                                                                           ```
                                                                                                                                                                                                           
                                                                                                                                                                                                           **Calculation:**
                                                                                                                                                                                                           ```
                                                                                                                                                                                                           Total = (base_price + per_unit_surcharges) × quantity + setup_charge
                                                                                                                                                                                                           Total = ($3.11 + $0.15 + $0.05) × 500 + $120
                                                                                                                                                                                                           Total = $3.31 × 500 + $120 = $1,655 + $120 = $1,775
                                                                                                                                                                                                           ```
                                                                                                                                                                                                           
                                                                                                                                                                                                           **Pros:**
                                                                                                                                                                                                           - Transparent to customer (they see setup charge separately)
                                                                                                                                                                                                           - - Accurate accounting (LogoMark knows setup was charged)
                                                                                                                                                                                                             - - Easy to modify per product
                                                                                                                                                                                                              
                                                                                                                                                                                                               - **Cons:**
                                                                                                                                                                                                               - - More line items in pricing display
                                                                                                                                                                                                                 - - Slightly more complex UI
                                                                                                                                                                                                                  
                                                                                                                                                                                                                   - ### Option B: Amortize Setup Across Quantity Breaks
                                                                                                                                                                                                                  
                                                                                                                                                                                                                   - **How it works:**
                                                                                                                                                                                                                   - - Calculate setup charge spread across the minimum quantity break
                                                                                                                                                                                                                     - - Update the quantity-break prices to include amortized setup
                                                                                                                                                                                                                       - - No separate "setup charge" visible; it's baked into the pricing
                                                                                                                                                                                                                        
                                                                                                                                                                                                                         - **Metafield structure:**
                                                                                                                                                                                                                         - ```json
                                                                                                                                                                                                                           {
                                                                                                                                                                                                                             "quantity_breaks": [
                                                                                                                                                                                                                               {
                                                                                                                                                                                                                                 "min_qty": 150,
                                                                                                                                                                                                                                 "base_price": 3.65,
                                                                                                                                                                                                                                 "two_sided_price": 3.80,
                                                                                                                                                                                                                                 "setup_amortized": true,
                                                                                                                                                                                                                                 "setup_per_unit": 0.8
                                                                                                                                                                                                                               }
                                                                                                                                                                                                                             ]
                                                                                                                                                                                                                           }
                                                                                                                                                                                                                           ```
                                                                                                                                                                                                                           
                                                                                                                                                                                                                           **Calculation:**
                                                                                                                                                                                                                           ```
                                                                                                                                                                                                                           Setup amortized cost per unit = $120 / 150 = $0.80 per unit
                                                                                                                                                                                                                           Adjusted base price at 150 qty = $3.48 + $0.80 = $4.28
                                                                                                                                                                                                                           ```
                                                                                                                                                                                                                           
                                                                                                                                                                                                                           **Pros:**
                                                                                                                                                                                                                           - Simpler pricing display (no separate line items)
                                                                                                                                                                                                                           - - Looks cleaner to customer
                                                                                                                                                                                                                             - - Standard promotional product practice
                                                                                                                                                                                                                              
                                                                                                                                                                                                                               - **Cons:**
                                                                                                                                                                                                                               - - Setup cost is hidden in the pricing
                                                                                                                                                                                                                                 - - More confusing when comparing with LogoMark's pricing
                                                                                                                                                                                                                                   - - Harder to adjust setup costs later
                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                     - ---
                                                                                                                                                                                                                                     
                                                                                                                                                                                                                                     ## Recommendation: Option A (Separate Setup Field)
                                                                                                                                                                                                                                     
                                                                                                                                                                                                                                     **We recommend Option A** for these reasons:
                                                                                                                                                                                                                                     
                                                                                                                                                                                                                                     1. **LogoMark Alignment:** LogoMark shows setup charges separately; matching this makes accounting clearer
                                                                                                                                                                                                                                     2. 2. **Flexibility:** Easy to add/remove/modify setup charges per product without recalculating pricing tables
                                                                                                                                                                                                                                        3. 3. **Transparency:** Customers and sales team see exactly what they're paying for
                                                                                                                                                                                                                                           4. 4. **Accuracy:** We must pay LogoMark for setup; separating it ensures it gets invoiced
                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                              5. ---
                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                              6. ## Implementation Specs for Custom App
                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                              7. ### Metafield Structure
                                                                                                                                                                                                                                             
                                                                                                                                                                                                                                              8. ```json
                                                                                                                                                                                                                                                 {
                                                                                                                                                                                                                                                   "product_id": "gid://shopify/Product/...",
                                                                                                                                                                                                                                                   "quantity_breaks": [
                                                                                                                                                                                                                                                     {
                                                                                                                                                                                                                                                       "min_qty": 150,
                                                                                                                                                                                                                                                       "max_qty": 249,
                                                                                                                                                                                                                                                       "base_price": 3.48
                                                                                                                                                                                                                                                     },
                                                                                                                                                                                                                                                     {
                                                                                                                                                                                                                                                       "min_qty": 250,
                                                                                                                                                                                                                                                       "max_qty": 499,
                                                                                                                                                                                                                                                       "base_price": 3.29
                                                                                                                                                                                                                                                     },
                                                                                                                                                                                                                                                     {
                                                                                                                                                                                                                                                       "min_qty": 500,
                                                                                                                                                                                                                                                       "base_price": 3.11
                                                                                                                                                                                                                                                     }
                                                                                                                                                                                                                                                   ],
                                                                                                                                                                                                                                                   "options": [
                                                                                                                                                                                                                                                     {
                                                                                                                                                                                                                                                       "id": "option_two_sided",
                                                                                                                                                                                                                                                       "name": "Two Sided Customization",
                                                                                                                                                                                                                                                       "type": "select",
                                                                                                                                                                                                                                                       "per_unit_surcharge": 0.15,
                                                                                                                                                                                                                                                       "setup_charge": 120,
                                                                                                                                                                                                                                                       "setup_description": "Mold for front and back side (one time $120)",
                                                                                                                                                                                                                                                       "requires_parent": null,
                                                                                                                                                                                                                                                       "display_order": 1
                                                                                                                                                                                                                                                     }
                                                                                                                                                                                                                                                   ]
                                                                                                                                                                                                                                                 }
                                                                                                                                                                                                                                                 ```
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ### Pricing Calculation Algorithm
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ```javascript
                                                                                                                                                                                                                                                 function calculatePrice(baseQty, selectedOptions) {
                                                                                                                                                                                                                                                   const qtyBreak = quantityBreaks.find(
                                                                                                                                                                                                                                                     b => qty >= b.min_qty && (b.max_qty ? qty <= b.max_qty : true)
                                                                                                                                                                                                                                                   );
                                                                                                                                                                                                                                                   const basePrice = qtyBreak.base_price;
                                                                                                                                                                                                                                                   let perUnitSurcharge = 0;
                                                                                                                                                                                                                                                   let totalSetupCharge = 0;

                                                                                                                                                                                                                                                   selectedOptions.forEach(optionId => {
                                                                                                                                                                                                                                                     const option = findOption(optionId);
                                                                                                                                                                                                                                                     perUnitSurcharge += option.per_unit_surcharge;
                                                                                                                                                                                                                                                     totalSetupCharge += option.setup_charge;
                                                                                                                                                                                                                                                   });

                                                                                                                                                                                                                                                   const unitPrice = basePrice + perUnitSurcharge;
                                                                                                                                                                                                                                                   const subtotal = unitPrice × baseQty;
                                                                                                                                                                                                                                                   const total = subtotal + totalSetupCharge;

                                                                                                                                                                                                                                                   return {
                                                                                                                                                                                                                                                     unitPrice,
                                                                                                                                                                                                                                                     subtotal,
                                                                                                                                                                                                                                                     setupCharge: totalSetupCharge,
                                                                                                                                                                                                                                                     total,
                                                                                                                                                                                                                                                     breakdown: {
                                                                                                                                                                                                                                                       basePrice,
                                                                                                                                                                                                                                                       perUnitSurcharge,
                                                                                                                                                                                                                                                       setupCharge: totalSetupCharge
                                                                                                                                                                                                                                                     }
                                                                                                                                                                                                                                                   };
                                                                                                                                                                                                                                                 }
                                                                                                                                                                                                                                                 ```
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ---
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ## Summary: What the App Must Handle
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 | Feature | Priority | Dog Tag | Vinyl | LogoMark |
                                                                                                                                                                                                                                                 |---------|----------|---------|-------|----------|
                                                                                                                                                                                                                                                 | Quantity breaks | CRITICAL | ✓ | ✓ | ✓ |
                                                                                                                                                                                                                                                 | Per-unit surcharges | CRITICAL | ✓ | ✓ | — |
                                                                                                                                                                                                                                                 | One-time setup charges | **CRITICAL** | ✓ | ✓ | ✓ |
                                                                                                                                                                                                                                                 | Cascading options | HIGH | — | ✓ | — |
                                                                                                                                                                                                                                                 | Location-based pricing | MEDIUM | — | — | ✓ |
                                                                                                                                                                                                                                                 | CSS consistency | HIGH | ✓ | ✓ | ✓ |
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ---
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 ## Next Steps
                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                 1. **Decide on setup charge handling:** Option A (separate field) vs Option B (amortized)
                                                                                                                                                                                                                                                 2.    - **Recommendation:** Option A
                                                                                                                                                                                                                                                       - 2. **Audit existing Tablepress CSS** to extract for replication
                                                                                                                                                                                                                                                         3. 3. **Create Metafield Schema** in Shopify admin
                                                                                                                                                                                                                                                            4. 4. **Build Custom App** with pricing calculation engine
                                                                                                                                                                                                                                                               5. 5. **Test with real product data** from each pattern
                                                                                                                                                                                                                                                                  6. 6. **Deploy and iterate** based on real customer orders
                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                     7. ---
                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                     8. ## References
                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                     9. - Dog Tag Product: https://www.promotionalkeychains.biz/collections/custom-dog-tags/products/brushed-antiqued-custom-designed-dog-tags
                                                                                                                                                                                                                                                                        - - Vinyl Keychain: https://www.promotionalkeychains.biz/collections/vinyl-keychains/products/dump-truck-vinyl-keychain-pk3978
                                                                                                                                                                                                                                                                          - - LogoMark Product: https://logomark.com/product/KP2444B
