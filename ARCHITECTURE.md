# PPO Logo Mark Integration - Technical Architecture

## Overview
This document describes the technical design and data flows for integrating Logo Mark products into the PPO Shopify store via PromoStandards SOAP APIs.

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    PPO Shopify Store                         │
│  (daznia-z0.myshopify.com)                                  │
└────────────────────┬────────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        │                         │
   ┌────▼──────────┐      ┌──────▼─────────┐
   │  Shopify REST │      │  Shopify REST   │
   │  API Products │      │  API Metafields │
   └────┬──────────┘      └──────┬─────────┘
        │                         │
   ┌────▼─────────────────────────▼──────────────┐
   │   Logo Mark SOAP Integration Layer          │
   │   (Node.js/PHP SOAP Client)                 │
   │                                             │
   │  - SOAP Request/Response Handling           │
   │  - Authentication (API Key)                 │
   │  - Data Transformation                      │
   │  - Error Handling & Retry Logic             │
   └────┬────────────────────────────────────────┘
        │
   ┌────▼──────────────────────────────────┐
   │  Logo Mark SOAP Endpoints              │
   │  (psapi.logomark.com)                  │
   │                                        │
   │  ✓ Inventory 2.0                       │
   │  ✓ Media Content 1.1                   │
   │  ✓ Order Status 1.0                    │
   │  ✓ Product Data 2.0                    │
   │  ✓ Product Pricing & Config 1.0        │
   │  ✓ Order Shipment Notification 1.0     │
   └────────────────────────────────────────┘
```

## Data Flow Layers

### Layer 1: Product Import Flow
Logo Mark APIs → SOAP Client → Transform → Shopify REST API → PPO Store

**Process:**
1. Product Data 2.0: Fetch product metadata (name, SKU, description, MOQ)
2. 2. Media 1.1: Fetch product images
   3. 3. Product Pricing & Config 1.0: Fetch pricing tiers and variant configuration
      4. 4. Transform: SKU shifting, AI description enhancement, Easify option mapping, Tablepress pricing tables
         5. 5. Create/Update: Post products to Shopify via REST API
            6. 6. Store Metadata: Use Shopify metafields to store Logo Mark references
              
               7. ### Layer 2: Stock Sync Flow
               8. Inventory 2.0 → SOAP Client → Transform → Shopify REST API → Product Variants
              
               9. **Process:**
               10. 1. Poll Inventory 2.0 endpoint (frequency: TBD by Michael)
                   2. 2. Transform stock levels to variant inventory
                      3. 3. Update via Shopify REST API
                        
                         4. ### Layer 3: Order Tracking Flow
                         5. Order Status 1.0 / Shipment Notification 1.0 → SOAP Client → Shopify REST API → Customer Accounts
                        
                         6. **Process:**
                         7. 1. Receive order status updates from Logo Mark
                            2. 2. Store in Shopify as order timeline/tracking
                               3. 3. Display in customer account portal
                                 
                                  4. ## SOAP Client Setup
                                 
                                  5. ### Authentication
                                  6. - Method: API Key in SOAP header
                                     - - Key: BA0F7D3C-6A14-47D0-AFAA-19067BDB3224
                                       - - Header Format: (TBD - await Logo Mark field spec)
                                        
                                         - ### Endpoints
                                         - | API | Service URL | Purpose |
                                         - |-----|------------|---------|
                                         - | Inventory 2.0 | https://psapi.logomark.com/LogomarkInventoryV2Service.svc | Stock levels |
                                         - | Media 1.1 | https://psapi.logomark.com/MediaContentService.svc | Product images |
                                         - | Order Status 1.0 | https://psapi.logomark.com/OrderStatusService.svc | Order tracking |
                                         - | Product Data 2.0 | https://psapi.logomark.com/ProductDataV2Service.svc | Product specs |
                                         - | Product Pricing & Config 1.0 | https://psapi.logomark.com/[TBD] | Pricing & variants |
                                         - | Order Shipment Notification 1.0 | https://psapi.logomark.com/[TBD] | Shipment updates |
                                        
                                         - ### Error Handling
                                         - - Timeout: 30 seconds (configurable)
                                           - - Retries: 3 attempts with exponential backoff
                                             - - Rate Limiting: Monitor response headers for rate limit guidance
                                               - - Logging: All requests/responses logged for debugging
                                                
                                                 - ## Data Transformation
                                                
                                                 - ### SKU Transformation
                                                 - Input:  PKACBB
                                                 - Logic:  Each character ASCII code + 1 (with wrapping: Z→A, 9→0)
                                                 - Output: PPDBCC
                                                
                                                 - Implementation: Custom transformer function with character validation.
                                                
                                                 - ### Price Mapping
                                                 - Logo Mark Data → Product Pricing & Config 1.0 (Quantity breaks)
                                                 - PPO Structure → Tablepress dynamic table
                                                 - - Rows = Quantity breaks (10, 25, 50, 100, 250, 500)
                                                   - - Columns = Easify option values (sizes, engraving areas)
                                                     - - Cell values = Final price
                                                      
                                                       - ### Easify Option Mapping
                                                       - If Logo Mark provides variant data (from field spec), map to Easify:
                                                       - - Parent options (e.g., Size)
                                                         - - Dependent options (e.g., Back-2x3 only when Size="Up to 2x3")
                                                           - - Surcharges per option value
                                                            
                                                             - If NOT provided, use category templates (to be defined in Phase 1).
                                                            
                                                             - ### Description Enhancement
                                                             - Logo Mark Input: "Belt Buckles with engraving"
                                                             - AI Enhancement (Claude API): "Premium custom belt buckles with full-color engraving. Make a statement with personalized buckles that reflect your unique style. Perfect for gifts or corporate branding."
                                                            
                                                             - ## Shopify Integration Points
                                                            
                                                             - ### Product Creation
                                                             - - Endpoint: POST /admin/api/2024-01/products.json
                                                               - - Fields: title, handle, vendor, bodyHtml, tags, metafields
                                                                
                                                                 - ### Inventory Management
                                                                 - - Endpoint: PUT /admin/api/2024-01/inventory_items/{id}.json
                                                                   - - Updates: tracked quantity, cost
                                                                    
                                                                     - ### Metafields - Store Logo Mark References
                                                                     - - logomark.product_id: Logo Mark product ID
                                                                       - - logomark.sku_original: Original Logo Mark SKU
                                                                         - - logomark.moq: Minimum Order Quantity
                                                                          
                                                                           - ## Orchestration & Scheduling
                                                                          
                                                                           - ### Approach Options
                                                                           - 1. GitHub Actions (recommend for MVP)
                                                                             2.    - Scheduled workflows for daily syncs
                                                                                   -    - Event-driven for manual triggers
                                                                                        -    - Cost: Free
                                                                                         
                                                                                             - 2. Make.com (existing integration with Salesforce)
                                                                                               3.    - Webhook-based for real-time updates
                                                                                                     -    - Preserves existing PPO Salesforce workflow
                                                                                                      
                                                                                                          - Decision: GitHub Actions for product import; Make.com for order tracking.
                                                                                                      
                                                                                                          - ## Data Quality Checks
                                                                                                      
                                                                                                          - ### Pre-Import Validation
                                                                                                          - - SKU format validation
                                                                                                            - - Image URL validation
                                                                                                              - - Price structure consistency
                                                                                                                - - MOQ >= 10 units
                                                                                                                  - - Description length (2-3 sentences)
                                                                                                                   
                                                                                                                    - ### Post-Import Validation
                                                                                                                    - - Product created in Shopify
                                                                                                                      - - All images attached
                                                                                                                        - - Pricing visible on storefront
                                                                                                                          - - Easify options functional
                                                                                                                            - - Quantity selector starts at MOQ
                                                                                                                             
                                                                                                                              - ## Known Constraints
                                                                                                                             
                                                                                                                              - 1. PromoStandards Standard: No custom extensions
                                                                                                                                2. 2. Easify Complexity: Waterfall dependent options require careful mapping
                                                                                                                                   3. 3. Image Handling: Quality/size from Logo Mark TBD
                                                                                                                                      4. 4. Pricing Sync: Manually managed Tablepress tables
                                                                                                                                         5. 5. Metadata: Brand/category mapping may require manual setup
                                                                                                                                           
                                                                                                                                            6. ## Next Steps
                                                                                                                                           
                                                                                                                                            7. 1. Receive Logo Mark Field Specification (Excel from Michael)
                                                                                                                                               2. 2. Finalize SOAP Client Implementation (Phase 1)
                                                                                                                                                  3. 3. Test Endpoints with sample data
                                                                                                                                                     4. 4. Iterate on Data Transformation based on real data
