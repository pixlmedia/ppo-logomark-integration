# PPO Logo Mark Integration - Project Briefing

**Project Status:** Pre-Implementation - Requirements gathering and planning complete
**Last Updated:** 2025-01-02
**Team:** Chris (Architect/Director), Claude (CTO/Technical Implementation)

---

## Executive Summary

Integrate Logo Mark products into the PPO (ppo.shop) Shopify store using PromoStandards SOAP APIs. Logo Mark will be a core supplier for the PPO re-launch. Products will use:
- **Dynamic pricing** from Tablepress tables (quantity breaks)
- - **Easify options** for product customization (waterfall-style dependent options)
  - - **AI-enhanced descriptions** to bridge bare-bones Logo Mark data with PPO's contemporary brand voice
   
    - **Scope:** PPO store only (no PK/PromotionalKeychains.biz changes)
   
    - ---

    ## Key Constraints & Decisions

    ### API Approach
    - **All Logo Mark APIs are SOAP-based** (not REST)
    - - SOAP authentication via **API Key** (provided: BA0F7D3C-6A14-47D0-AFAA-19067BDB3224)
      - - No custom extensions - standard PromoStandards schema
       
        - ### Which APIs to Integrate
        - We are integrating **6 of 8** available APIs (excluding Purchase Order & Invoices which are handled manually):
        - 1. **Inventory 2.0** - Stock levels
          2. 2. **Order Status 1.0** - Order tracking
             3. 3. **Order Shipment Notification 1.0** - Shipment updates
                4. 4. **Media Content 1.1** - Product images
                   5. 5. **Product Pricing and Configuration 1.0** - Core pricing/variant data
                      6. 6. **Product Data 2.0** - Quantity breaks and detailed specs
                        
                         7. ### Easify Options Architecture
                         8. Logo Mark products will NOT use PPO's design tool. Instead:
                         9. - **Use Easify options exclusively** for customization
                            - - Create **custom option sets per product category** (if Logo Mark data doesn't provide option details)
                              - - **Easify enables waterfall-style dependent options** (e.g., Back-2x3 only shows when Size="Up to 2"x 3")
                               
                                - **Status:** Easify options can be auto-created if we receive detailed Logo Mark configuration data. If not, we may need manual setup or templates.
                               
                                - ### Pricing Logic
                                - PPO's pricing system is **two-tier**:
                               
                                - 1. **Base pricing from Tablepress** (dynamic table per product)
                                  2.    - Columns = Easify option values (e.g., "Up to 2x3"", "Up to 3x4"")
                                        -    - Rows = Quantity breaks (MOQ varies by product: 10-500+)
                                             -    - Manually edited/managed currently
                                              
                                                  - 2. **Easify option surcharges** (applied on top of base price)
                                                    3.    - Example: Size selection +$0 or +$6.27
                                                          -    - Back-side engraving option +$0 or +$0.80
                                                               -    - Additional upcharges for molds, graphics, etc.
                                                                
                                                                    - **Logo Mark provides:** Quantity-based pricing from Product Pricing and Configuration API
                                                                    - **We create:** Tablepress table structure + Easify option pricing
                                                                
                                                                    - ### SKU Format for Logo Mark Products
                                                                    - **PPO Convention:** `PPO-[shifted Logo Mark SKU]`
                                                                    - - "Shift" = move each character right by 1 in ASCII (A→B, Z→A, 0→1, 9→0)
                                                                      - - Example: PKACBB becomes PPDBCC
                                                                       
                                                                        - **Status:** Shifting logic is straightforward; can be automated in SOAP transformation layer
                                                                       
                                                                        - ### Description Strategy
                                                                        - Logo Mark provides **bare-bones descriptions**. PPO requires:
                                                                        - - **Length:** 2-3 sentences (short, energetic, professional)
                                                                          - - **Tone:** Contemporary brand voice (not editorial like PK)
                                                                            - - **Enhancement:** Use AI to flesh out between bare-bones and detailed
                                                                             
                                                                              - **Approach:** AI will enhance Logo Mark descriptions to PPO's brand voice during product import
                                                                             
                                                                              - ### Sample Pricing Not Needed
                                                                              - PK offers Sample units ($1-$5) to show Google ads lower price. PPO won't use samples for Logo Mark products. All Logo Mark products = bulk-only with MOQ.
                                                                             
                                                                              - ---

                                                                              ## Technical Stack

                                                                              - **SOAP Client:** PHP/Node.js SOAP library (TBD based on infrastructure)
                                                                              - - **Shopify Integration:** Shopify REST API for product creation/updates
                                                                                - - **Pricing Sync:** Tablepress entries + Easify option value pricing
                                                                                  - - **Metadata:** Shopify metafields for Logo Mark API references
                                                                                    - - **Orchestration:** GitHub Actions or Make.com workflow
                                                                                      - - **AI Enhancement:** TBD (OpenAI/Claude API for description improvement)
                                                                                       
                                                                                        - ---

                                                                                        ## Current Product Examples

                                                                                        ### PK (Reference - Easify-Only Product)
                                                                                        - **Product:** Custom Belt Buckles Antiqued (PKCBBP)
                                                                                        - - **MOQ:** 50 units
                                                                                          - - **Easify Options:**
                                                                                            -   1. Size (parent) → triggers dependent options
                                                                                                2.   2. Back-2x3 (shows if Size="Up to 2"x 3"") → +$0 or +$0.80
                                                                                                     3.   3. Back-3x4 (shows if Size="Up to 3"x 4"")
                                                                                                          4.   4. Mold options (4 variants for different sizes)
                                                                                                               5.   5. Graphic options (2 additional graphics)
                                                                                                                    6.   6. Instructions (informational)
                                                                                                                         7. - **Tablepress:** Quantity × Size engraving area = price
                                                                                                                            -   - Quantities: 50, 100, 250, 500
                                                                                                                                -   - Sizes: Up to 2"×3", Up to 3"×4"
                                                                                                                                    -   - Pricing varies per option combo
                                                                                                                                     
                                                                                                                                        - ### PPO (Development - Same Structure)
                                                                                                                                        - - **Status:** Under development
                                                                                                                                          - - **Known Issues to Fix:**
                                                                                                                                            -   - Easify options not yet implemented
                                                                                                                                                -   - Quantity selector starts at 1 (should start at MOQ=50)
                                                                                                                                                    -   - CSS spacing issues on delivery timeline widget
                                                                                                                                                        -   - Single product image (need separate images like PK)
                                                                                                                                                            -   - Description copy is sparse (needs AI enhancement)
                                                                                                                                                                - - **What's Ready:**
                                                                                                                                                                  -   - Tablepress pricing table template
                                                                                                                                                                      -   - "Free Proof and Quote" button
                                                                                                                                                                          -   - Contemporary brand styling
                                                                                                                                                                              -   - FAQ sections on each product page
                                                                                                                                                                                  -   - Production specifications collapsible sections
                                                                                                                                                                                   
                                                                                                                                                                                      - ---
                                                                                                                                                                                      
                                                                                                                                                                                      ## Logo Mark Endpoint Details
                                                                                                                                                                                      
                                                                                                                                                                                      ### Authentication
                                                                                                                                                                                      - **Key:** BA0F7D3C-6A14-47D0-AFAA-19067BDB3224
                                                                                                                                                                                      - - **Method:** API Key header (SOAP auth)
                                                                                                                                                                                       
                                                                                                                                                                                        - ### Endpoints (All SOAP)
                                                                                                                                                                                        - ```
                                                                                                                                                                                          Inventory 2.0: https://psapi.logomark.com/LogomarkInventoryV2Service.svc
                                                                                                                                                                                          Media 1.1: https://psapi.logomark.com/MediaContentService.svc
                                                                                                                                                                                          Order Status 1.0: https://psapi.logomark.com/OrderStatusService.svc
                                                                                                                                                                                          Product Data 2.0: https://psapi.logomark.com/ProductDataV2Service.svc
                                                                                                                                                                                          Product Pricing & Config 1.0: https://psapi.logomark.com/[endpoint-TBD]
                                                                                                                                                                                          Order Shipment Notification 1.0: https://psapi.logomark.com/[endpoint-TBD]
                                                                                                                                                                                          ```
                                                                                                                                                                                          
                                                                                                                                                                                          ### Field Support
                                                                                                                                                                                          - Logo Mark supports all **Required** PromoStandards fields + some **Optional** fields
                                                                                                                                                                                          - - **Pending:** Detailed field specification from Logo Mark (promised by end of week as Excel file)
                                                                                                                                                                                            - - No proprietary extensions; clean data normalized to PromoStandards
                                                                                                                                                                                             
                                                                                                                                                                                              - ---
                                                                                                                                                                                              
                                                                                                                                                                                              ## Implementation Roadmap
                                                                                                                                                                                              
                                                                                                                                                                                              ### Phase 1: Foundation (Weeks 1-2)
                                                                                                                                                                                              - [ ] Receive Logo Mark field specification Excel file
                                                                                                                                                                                              - [ ] - [ ] Build SOAP client + authentication
                                                                                                                                                                                              - [ ] - [ ] Create Product Data 2.0 mapping (SKU, pricing, MOQ)
                                                                                                                                                                                              - [ ] - [ ] Test basic product import flow
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ### Phase 2: Pricing & Options (Weeks 3-4)
                                                                                                                                                                                              - [ ] - [ ] Implement Tablepress table auto-generation from Product Data
                                                                                                                                                                                              - [ ] - [ ] Design Easify option schema (create templates or auto-generate)
                                                                                                                                                                                              - [ ] - [ ] Map Easify option pricing from Logo Mark data
                                                                                                                                                                                              - [ ] - [ ] Build quantity selector MOQ logic
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ### Phase 3: Media & Descriptions (Weeks 5-6)
                                                                                                                                                                                              - [ ] - [ ] Implement Media 1.1 image sync
                                                                                                                                                                                              - [ ] - [ ] Build AI description enhancement pipeline
                                                                                                                                                                                              - [ ] - [ ] Create product page template variations
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ### Phase 4: Inventory & Orders (Weeks 7-8)
                                                                                                                                                                                              - [ ] - [ ] Implement Inventory 2.0 stock sync
                                                                                                                                                                                              - [ ] - [ ] Implement Order Status 1.0 tracking
                                                                                                                                                                                              - [ ] - [ ] Implement Order Shipment Notification 1.0
                                                                                                                                                                                              - [ ] - [ ] Connect to existing Make.com Salesforce workflow
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ### Phase 5: Testing & Launch (Weeks 9-10)
                                                                                                                                                                                              - [ ] - [ ] Full product import testing
                                                                                                                                                                                              - [ ] - [ ] PPO site testing (frontend validation)
                                                                                                                                                                                              - [ ] - [ ] Logo Mark data quality review
                                                                                                                                                                                              - [ ] - [ ] Go-live coordination
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ---
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ## Known Unknowns & Questions
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] 1. **Easify Option Details:** Will Logo Mark API provide option/variant configuration data, or must we create templates?
                                                                                                                                                                                              - [ ] 2. **Image Quality:** How many images per product? What formats/sizes from Logo Mark?
                                                                                                                                                                                              - [ ] 3. **Quantity Break Variations:** Will MOQs and pricing tiers vary significantly across product categories?
                                                                                                                                                                                              - [ ] 4. **Metadata Requirements:** What additional PPO-specific metadata (brand, category, collection) needs manual mapping?
                                                                                                                                                                                              - [ ] 5. **AI Enhancement API:** Which AI service for description enhancement? Cost/latency requirements?
                                                                                                                                                                                              - [ ] 6. **Update Frequency:** How often should Logo Mark data sync (daily, weekly, on-demand)?
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ---
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ## Success Criteria
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] - ✅ All Logo Mark products available on PPO with accurate pricing
                                                                                                                                                                                              - [ ] - ✅ Tablepress tables auto-generated from Logo Mark data
                                                                                                                                                                                              - [ ] - ✅ Easify options properly configured (waterfall dependent options work)
                                                                                                                                                                                              - [ ] - ✅ Product images display correctly
                                                                                                                                                                                              - [ ] - ✅ Inventory levels sync from Logo Mark
                                                                                                                                                                                              - [ ] - ✅ Order status visible in customer accounts
                                                                                                                                                                                              - [ ] - ✅ AI-enhanced descriptions meet PPO brand voice
                                                                                                                                                                                              - [ ] - ✅ No manual data entry required (except Easify setup if unavoidable)
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ---
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ## File Structure (to be created)
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ```
                                                                                                                                                                                              - [ ] ppo-logomark-integration/
                                                                                                                                                                                              - [ ] ├── PROJECT_BRIEFING.md (this file)
                                                                                                                                                                                              - [ ] ├── ARCHITECTURE.md (technical design & data flows)
                                                                                                                                                                                              - [ ] ├── API_SPECIFICATION.md (Logo Mark API details & mapping)
                                                                                                                                                                                              - [ ] ├── DATA_MAPPING.md (transformation logic Logo Mark → PPO)
                                                                                                                                                                                              - [ ] ├── IMPLEMENTATION_ROADMAP.md (detailed task breakdown)
                                                                                                                                                                                              - [ ] ├── src/
                                                                                                                                                                                              - [ ] │   ├── soap-client/ (SOAP API integration)
                                                                                                                                                                                              - [ ] │   ├── product-sync/ (product import logic)
                                                                                                                                                                                              - [ ] │   ├── tablepress/ (Tablepress table generation)
                                                                                                                                                                                              - [ ] │   ├── easify/ (Easify option creation/management)
                                                                                                                                                                                              - [ ] │   ├── ai-enhancement/ (description enhancement)
                                                                                                                                                                                              - [ ] │   └── inventory-sync/ (stock level updates)
                                                                                                                                                                                              - [ ] ├── config/
                                                                                                                                                                                              - [ ] │   ├── credentials.example.json
                                                                                                                                                                                              - [ ] │   └── logomark-mapping.json
                                                                                                                                                                                              - [ ] ├── tests/
                                                                                                                                                                                              - [ ] │   ├── unit/
                                                                                                                                                                                              - [ ] │   └── integration/
                                                                                                                                                                                              - [ ] └── docs/
                                                                                                                                                                                              - [ ]     ├── API_REFERENCE.md
                                                                                                                                                                                              - [ ]     ├── TROUBLESHOOTING.md
                                                                                                                                                                                              - [ ]     └── RUNBOOK.md
                                                                                                                                                                                              - [ ] ```
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ---
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] ## Notes for Future Sessions
                                                                                                                                                                                             
                                                                                                                                                                                              - [ ] - **Context Reset:** This briefing document serves as the permanent record. Always load this first when resuming work.
                                                                                                                                                                                              - [ ] - **Shopify Admin:** PK store = pkdotbiz.myshopify.com, PPO = daznia-z0.myshopify.com
                                                                                                                                                                                              - [ ] - **API Key Stored:** (see credentials section above)
                                                                                                                                                                                              - [ ] - **Easify Complexity:** Already explored PKCBBP option set; be prepared for conditional logic in Easify
                                                                                                                                                                                              - [ ] - **GitHub:** This repo is the central reference; keep all specs, decisions, and progress here
