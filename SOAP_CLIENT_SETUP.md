# SOAP Client Setup & Authentication

## Overview
This document covers the setup, configuration, and testing procedures for integrating with Logo Mark's SOAP-based APIs.

## 1. SOAP Client Library Selection

### Recommended Options

#### Option A: Node.js (Recommended for Speed)
**Library**: `soap` (npm package)
```bash
npm install soap
```

**Pros**:
- Native JavaScript integration
- - Easy to deploy to GitHub Actions
  - - Good error handling
    - - Active maintenance
     
      - **Cons**:
      - - Smaller ecosystem than PHP
        - - May need custom extensions for complex WSDLs
         
          - #### Option B: PHP
          - **Library**: PHP's built-in `SoapClient` class or `easywsdl/soap-client`
          - ```bash
            composer require easywsdl/soap-client
            ```

            **Pros**:
            - Mature, stable library
            - - Excellent WSDL parsing
              - - Good in legacy systems
               
                - **Cons**:
                - - Need PHP runtime
                  - - Less flexible for modern dev workflows
                   
                    - ### Decision: Node.js (using `soap` npm package)
                    - **Reasoning**: Faster development, GitHub Actions integration, modern JavaScript ecosystem.
                   
                    - ---

                    ## 2. Authentication Setup

                    ### API Key
                    ```
                    Provider: Logo Mark
                    Key: BA0F7D3C-6A14-47D0-AFAA-19067BDB3224
                    Method: SOAP Header Authentication
                    ```

                    ### Environment Configuration
                    Create `.env` file (DO NOT COMMIT):
                    ```
                    LOGOMARK_API_KEY=BA0F7D3C-6A14-47D0-AFAA-19067BDB3224
                    LOGOMARK_API_BASE_URL=https://psapi.logomark.com
                    NODE_ENV=development
                    LOG_LEVEL=debug
                    ```

                    Create `config/.env.example` (safe to commit):
                    ```
                    LOGOMARK_API_KEY=<your-key-here>
                    LOGOMARK_API_BASE_URL=https://psapi.logomark.com
                    NODE_ENV=development
                    LOG_LEVEL=debug
                    ```

                    ---

                    ## 3. SOAP Header Authentication

                    ### Expected Format (TBD - Awaiting Logo Mark Spec)

                    Based on PromoStandards standard, likely structure:
                    ```xml
                    <soapenv:Header>
                      <wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
                        <wsse:UsernameToken>
                          <wsse:Username>API_KEY</wsse:Username>
                        </wsse:UsernameToken>
                      </wsse:Security>
                    </soapenv:Header>
                    ```

                    ### Node.js Implementation Template
                    ```javascript
                    const soap = require('soap');
                    const fs = require('fs');

                    const API_KEY = process.env.LOGOMARK_API_KEY;
                    const BASE_URL = process.env.LOGOMARK_API_BASE_URL;

                    async function createSoapClient(serviceUrl, serviceName) {
                      try {
                        const client = await soap.createClientAsync(serviceUrl, {
                          // WSDL options
                          wsdl_headers: {
                            'Authorization': `Bearer ${API_KEY}`
                          }
                        });

                        // Add custom SOAP header
                        const soapHeader = {
                          Security: {
                            UsernameToken: {
                              Username: API_KEY
                            }
                          }
                        };

                        client.addSoapHeader(soapHeader, '', 'wsse',
                          'http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd');

                        console.log(`✓ SOAP client created for ${serviceName}`);
                        return client;
                      } catch (error) {
                        console.error(`✗ Failed to create SOAP client for ${serviceName}:`, error.message);
                        throw error;
                      }
                    }

                    module.exports = { createSoapClient };
                    ```

                    ---

                    ## 4. API Endpoints & WSDL URLs

                    ### Active Endpoints
                    | Service | WSDL URL | Method | Status |
                    |---------|----------|--------|--------|
                    | Inventory 2.0 | https://psapi.logomark.com/LogomarkInventoryV2Service.svc?wsdl | GetInventory | ✓ Active |
                    | Media 1.1 | https://psapi.logomark.com/MediaContentService.svc?wsdl | GetMedia | ✓ Active |
                    | Order Status 1.0 | https://psapi.logomark.com/OrderStatusService.svc?wsdl | GetOrderStatus | ✓ Active |
                    | Product Data 2.0 | https://psapi.logomark.com/ProductDataV2Service.svc?wsdl | GetProducts | ✓ Active |
                    | Product Pricing & Config 1.0 | https://psapi.logomark.com/[TBD].svc?wsdl | GetPricing | TBD |
                    | Order Shipment Notification 1.0 | https://psapi.logomark.com/[TBD].svc?wsdl | GetShipments | TBD |

                    **Note**: TBD endpoints to be provided by Michael with field specification.

                    ---

                    ## 5. Connection Testing Checklist

                    ### Pre-Flight Checks
                    - [ ] API key is valid and non-empty
                    - [ ] - [ ] Network connectivity to psapi.logomark.com (test with `curl -I https://psapi.logomark.com`)
                    - [ ] - [ ] Node.js 14+ installed (`node -v`)
                    - [ ] - [ ] Dependencies installed (`npm install`)
                    - [ ] - [ ] Environment variables loaded (`.env` file exists)
                   
                    - [ ] ### Test 1: WSDL Parsing
                    - [ ] ```javascript
                    - [ ] // test/wsdl-parsing.js
                    - [ ] const soap = require('soap');
                   
                    - [ ] async function testWSDLParsing() {
                    - [ ]   const endpoints = [
                    - [ ]       'https://psapi.logomark.com/LogomarkInventoryV2Service.svc?wsdl',
                    - [ ]       'https://psapi.logomark.com/MediaContentService.svc?wsdl',
                    - [ ]       'https://psapi.logomark.com/OrderStatusService.svc?wsdl',
                    - [ ]       'https://psapi.logomark.com/ProductDataV2Service.svc?wsdl'
                    - [ ]     ];
                   
                    - [ ]   for (const url of endpoints) {
                    - [ ]       try {
                    - [ ]         const client = await soap.createClientAsync(url);
                    - [ ]           console.log(`✓ ${url} - WSDL parsed successfully`);
                    - [ ]             console.log(`  Available methods:`, Object.keys(client.describe()[Object.keys(client.describe())[0]][Object.keys(client.describe()[Object.keys(client.describe())[0]][0]][0]));
                    - [ ]             } catch (error) {
                    - [ ]               console.error(`✗ ${url} - WSDL parsing failed:`, error.message);
                    - [ ]               }
                    - [ ]             }
                    - [ ]         }
                   
                    - [ ]     testWSDLParsing();
                    - [ ] ```
                   
                    - [ ] **Expected Output**:
                    - [ ] ```
                    - [ ] ✓ https://psapi.logomark.com/LogomarkInventoryV2Service.svc?wsdl - WSDL parsed successfully
                    - [ ]   Available methods: [ 'GetInventory', 'GetInventoryAsync' ]
                    - [ ]   ```
                   
                    - [ ]   ### Test 2: Authentication
                    - [ ]   ```javascript
                    - [ ]   // test/authentication.js
                    - [ ]   const { createSoapClient } = require('../src/soap-client');
                   
                    - [ ]   async function testAuthentication() {
                    - [ ]     try {
                    - [ ]     const client = await createSoapClient(
                    - [ ]       'https://psapi.logomark.com/ProductDataV2Service.svc?wsdl',
                    - [ ]         'Product Data 2.0'
                    - [ ]         );
                   
                    - [ ]             // Simple test call with minimal params
                    - [ ]             const result = await client.GetProductsAsync({
                    - [ ]               apiKey: process.env.LOGOMARK_API_KEY,
                    - [ ]                 pageIndex: 0,
                    - [ ]                   pageSize: 1 // Minimal request
                    - [ ]                   });
                   
                    - [ ]                       console.log('✓ Authentication successful');
                    - [ ]                       console.log('Response:', JSON.stringify(result[0], null, 2).substring(0, 500) + '...');
                    - [ ]                     } catch (error) {
                    - [ ]                     if (error.message.includes('Unauthorized') || error.message.includes('403')) {
                    - [ ]                       console.error('✗ Authentication FAILED - Invalid API key');
                    - [ ]                       } else {
                    - [ ]                         console.error('✗ Unexpected error:', error.message);
                    - [ ]                         }
                    - [ ]                       }
                    - [ ]                   }
                   
                    - [ ]               testAuthentication();
                    - [ ]           ```
                   
                    - [ ]       ### Test 3: Timeout & Retry Logic
                    - [ ]   ```javascript
                    - [ ]   // test/timeout-handling.js
                    - [ ]   const { createSoapClient } = require('../src/soap-client');
                   
                    - [ ]   async function testTimeoutHandling() {
                    - [ ]     const client = await createSoapClient(
                    - [ ]     'https://psapi.logomark.com/ProductDataV2Service.svc?wsdl',
                    - [ ]     'Product Data 2.0'
                    - [ ]   );
                   
                    - [ ]     // Set timeout to 5 seconds
                    - [ ]   client.setTimeout(5000);
                   
                    - [ ]     try {
                    - [ ]     const result = await client.GetProductsAsync({
                    - [ ]       apiKey: process.env.LOGOMARK_API_KEY,
                    - [ ]         pageIndex: 0,
                    - [ ]           pageSize: 100
                    - [ ]           });
                    - [ ]           console.log('✓ Request completed within timeout');
                    - [ ]         } catch (error) {
                    - [ ]         if (error.code === 'ETIMEDOUT' || error.message.includes('timeout')) {
                    - [ ]           console.log('⚠ Timeout occurred (expected for testing)');
                    - [ ]           } else {
                    - [ ]             console.error('✗ Unexpected error:', error.message);
                    - [ ]             }
                    - [ ]           }
                    - [ ]       }
                   
                    - [ ]   testTimeoutHandling();
                    - [ ]   ```
                   
                    - [ ]   ---
                   
                    - [ ]   ## 6. Error Handling Strategy
                   
                    - [ ]   ### Common SOAP Errors
                   
                    - [ ]   | Error Code | Cause | Action |
                    - [ ]   |-----------|-------|--------|
                    - [ ]   | 401 | Invalid API key | Check .env file, renew key with Michael |
                    - [ ]   | 403 | Forbidden (rate limited) | Wait 60s, retry with backoff |
                    - [ ]   | 404 | WSDL not found | Verify endpoint URL (check with Michael) |
                    - [ ]   | 500 | Server error | Log and retry (max 3x) |
                    - [ ]   | ENOTFOUND | Network unreachable | Check connectivity, retry |
                    - [ ]   | ETIMEDOUT | Request timeout (>30s) | Retry with backoff, check Logo Mark status |
                   
                    - [ ]   ### Retry Logic Template
                    - [ ]   ```javascript
                    - [ ]   async function callSOAPWithRetry(client, method, params, maxRetries = 3) {
                    - [ ]     let lastError;
                   
                    - [ ]     for (let attempt = 1; attempt <= maxRetries; attempt++) {
                    - [ ]     try {
                    - [ ]       console.log(`Attempt ${attempt}/${maxRetries}: ${method}`);
                   
                    - [ ]               const result = await client[`${method}Async`](params);
                    - [ ]                 console.log(`✓ ${method} succeeded`);
                    - [ ]                   return result[0]; // SOAP responses are wrapped in array
                   
                    - [ ]                         } catch (error) {
                    - [ ]                           lastError = error;
                   
                    - [ ]                                   // Determine if retryable
                    - [ ]                                     const isRetryable =
                    - [ ]                                         error.code === 'ETIMEDOUT' ||
                    - [ ]                                             error.code === 'ECONNRESET' ||
                    - [ ]                                                 (error.response && error.response.statusCode >= 500);
                   
                    - [ ]                                                         if (isRetryable && attempt < maxRetries) {
                    - [ ]                                                             const delay = Math.pow(2, attempt - 1) * 1000; // Exponential backoff
                    - [ ]                                                                 console.warn(`⚠ Retryable error: ${error.message}. Waiting ${delay}ms...`);
                    - [ ]                                                                     await new Promise(resolve => setTimeout(resolve, delay));
                    - [ ]                                                                       } else {
                    - [ ]                                                                           console.error(`✗ ${method} failed (non-retryable):`, error.message);
                    - [ ]                                                                               break;
                    - [ ]                                                                                 }
                    - [ ]                                                                                 }
                    - [ ]                                                                               }
                   
                    - [ ]                                                                               throw lastError;
                    - [ ]                                                                           }
                   
                    - [ ]                                                                       module.exports = { callSOAPWithRetry };
                    - [ ]                                                                   ```
                   
                    - [ ]                                                               ---
                   
                    - [ ]                                                           ## 7. Logging & Debugging
                   
                    - [ ]                                                       ### Setup Logging
                    - [ ]                                                   ```javascript
                    - [ ]                                               // src/logger.js
                    - [ ]                                           const log4js = require('log4js');
                   
                    - [ ]                                       log4js.configure({
                    - [ ]                                     appenders: {
                    - [ ]                                     console: { type: 'console' },
                    - [ ]                                     file: { type: 'file', filename: 'logs/soap-integration.log' }
                    - [ ]                                   },
                    - [ ]                                 categories: {
                    - [ ]                                 default: { appenders: ['console', 'file'], level: process.env.LOG_LEVEL || 'info' }
                    - [ ]                               }
                    - [ ]                           });
                   
                    - [ ]                       const logger = log4js.getLogger();
                   
                    - [ ]                   module.exports = logger;
                    - [ ]               ```
                   
                    - [ ]           ### Log All SOAP Requests/Responses
                    - [ ]       ```javascript
                    - [ ]   // src/soap-client.js (with logging)
                    - [ ]   const logger = require('./logger');
                   
                    - [ ]   async function createSoapClient(serviceUrl, serviceName) {
                    - [ ]     const client = await soap.createClientAsync(serviceUrl);
                   
                    - [ ]     // Log all requests
                    - [ ]   client.on('request', (envelope) => {
                    - [ ]       logger.debug(`[${serviceName}] SOAP Request:`, envelope.substring(0, 500) + '...');
                    - [ ]     });
                   
                    - [ ]     // Log all responses
                    - [ ]   client.on('response', (envelope) => {
                    - [ ]       logger.debug(`[${serviceName}] SOAP Response:`, envelope.substring(0, 500) + '...');
                    - [ ]     });
                   
                    - [ ]     return client;
                    - [ ] }
                    - [ ] ```
                   
                    - [ ] ### Audit Trail
                    - [ ] All SOAP calls should be logged with:
                    - [ ] - Timestamp
                    - [ ] - Service name
                    - [ ] - Method called
                    - [ ] - Request parameters (sanitized - no API key in logs!)
                    - [ ] - Response status (success/error)
                    - [ ] - Execution time
                   
                    - [ ] ---
                   
                    - [ ] ## 8. GitHub Actions Workflow Example
                   
                    - [ ] ### File: `.github/workflows/test-soap-connection.yml`
                    - [ ] ```yaml
                    - [ ] name: Test SOAP Connection
                   
                    - [ ] on:
                    - [ ]   workflow_dispatch:  # Manual trigger
                    - [ ]     schedule:
                    - [ ]     - cron: '0 */6 * * *'  # Every 6 hours
                   
                    - [ ] jobs:
                    - [ ]   test:
                    - [ ]       runs-on: ubuntu-latest
                   
                    - [ ]           steps:
                    - [ ]             - uses: actions/checkout@v3
                   
                    - [ ]                     - name: Setup Node.js
                    - [ ]                         uses: actions/setup-node@v3
                    - [ ]                             with:
                    - [ ]                                   node-version: '18'
                   
                    - [ ]                                           - name: Install dependencies
                    - [ ]                                               run: npm install
                   
                    - [ ]                                                       - name: Run WSDL parsing test
                    - [ ]                                                           run: node test/wsdl-parsing.js
                   
                    - [ ]                                                                   - name: Run authentication test
                    - [ ]                                                                       env:
                    - [ ]                                                                             LOGOMARK_API_KEY: ${{ secrets.LOGOMARK_API_KEY }}
                    - [ ]                                                                                 run: node test/authentication.js
                   
                    - [ ]                                                                                         - name: Upload logs
                    - [ ]                                                                                             if: always()
                    - [ ]                                                                                                 uses: actions/upload-artifact@v3
                    - [ ]                                                                                                     with:
                    - [ ]                                                                                                           name: soap-logs
                    - [ ]                                                                                                                 path: logs/
                    - [ ]                                                                                                             ```
                   
                    - [ ]                                                                                                         ---
                   
                    - [ ]                                                                                                     ## 9. Deployment Checklist
                   
                    - [ ]                                                                                                 Before going to production:
                    - [ ]                                                                                             - [ ] All WSDL endpoints parsing correctly
                    - [ ]                                                                                         - [ ] Authentication test passes
                    - [ ]                                                                                     - [ ] Timeout handling verified (requests complete in < 30s)
                    - [ ]                                                                                 - [ ] Retry logic tested (force timeout, verify backoff)
                    - [ ]                                                                             - [ ] Error logging working (check logs/ directory)
                    - [ ]                                                                         - [ ] Sensitive data NOT logged (no API keys in logs)
                    - [ ]                                                                     - [ ] GitHub Actions workflow passes
                    - [ ]                                                                 - [ ] Documentation updated with any custom header formats
                    - [ ]                                                             - [ ] Team trained on troubleshooting procedures
                   
                    - [ ]                                                         ---
                   
                    - [ ]                                                     ## 10. Troubleshooting
                   
                    - [ ]                                                 ### Issue: "WSDL fetch failed"
                    - [ ]                                             **Causes**:
                    - [ ]                                         - WSDL URL incorrect
                    - [ ]                                     - Network firewall blocking Logo Mark domain
                    - [ ]                                 - Logo Mark service down
                   
                    - [ ]                             **Solution**:
                    - [ ]                         ```bash
                    - [ ]                     # Test connectivity
                    - [ ]                 curl -I https://psapi.logomark.com/ProductDataV2Service.svc?wsdl
                   
                    - [ ]             # If 403/404, contact Michael
                    - [ ]         # If timeout, check firewall rules
                    - [ ]     ```
                   
                    - [ ] ### Issue: "Unauthorized (401)"
                    - [ ] **Cause**: Invalid or expired API key
                   
                    - [ ] **Solution**:
                    - [ ] 1. Verify API key in `.env` file matches `BA0F7D3C-6A14-47D0-AFAA-19067BDB3224`
                    - [ ] 2. Check if key has been revoked (contact Michael)
                    - [ ] 3. Verify SOAP header format matches Logo Mark's spec
                   
                    - [ ] ### Issue: "Request timeout"
                    - [ ] **Causes**:
                    - [ ] - Large dataset (too many products)
                    - [ ] - Network latency to Logo Mark servers
                    - [ ] - Logo Mark service slow
                   
                    - [ ] **Solution**:
                    - [ ] 1. Reduce pagination size (lower pageSize)
                    - [ ] 2. Increase timeout from 30s to 60s (if acceptable)
                    - [ ] 3. Implement async queue to process products in batches
                   
                    - [ ] ### Issue: "Response parsing error"
                    - [ ] **Cause**: Unexpected XML structure in response
                   
                    - [ ] **Solution**:
                    - [ ] 1. Log full SOAP response to file
                    - [ ] 2. Compare with WSDL schema
                    - [ ] 3. Contact Michael if response format differs from spec
                   
                    - [ ] ---
                   
                    - [ ] ## 11. Next Steps
                   
                    - [ ] 1. [ ] **Receive Logo Mark Field Specification** (Excel - end of week)
                    - [ ] 2. [ ] **Confirm SOAP Header Format** (clarify with Michael)
                    - [ ] 3. [ ] **Set up test environment** (Node.js, dependencies, .env)
                    - [ ] 4. [ ] **Create SOAP client library** with authentication
                    - [ ] 5. [ ] **Run connection tests** (WSDL parsing, auth, timeouts)
                    - [ ] 6. [ ] **Implement logging & monitoring**
                    - [ ] 7. [ ] **Deploy to GitHub Actions** for automated testing
                    - [ ] 
