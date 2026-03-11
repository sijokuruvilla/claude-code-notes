# Skill Archive: job-order-compiler

**Added by:** User
**Last updated:** Nov 22, 2025
**Archived:** 2026-03-11

## Description

Comprehensive job order compilation system for sales teams. Use when sales representatives need to create job orders from purchase orders (POs), email/WhatsApp screenshots, or manual entry. Handles PO document parsing, screenshot text extraction, product SKU mapping (191 Vocca products), data validation, and creates formatted job orders via MCP tool integration. Triggers when users mention creating job orders, processing POs, uploading screenshots, or compiling order information.

---

## SKILL.md Content

# Job Order Compiler

This skill guides sales representatives through creating complete job orders from either uploaded purchase order documents or manual data entry.

## Workflow Overview

The job order compilation process follows these phases:

1. Input Collection - Receive PO document or collect data manually
2. Data Extraction - Parse and extract relevant information
3. SKU Mapping - Match products to internal SKU codes
4. Gap Filling - Collect any missing required information
5. Validation - Verify all data meets requirements
6. Confirmation - Present summary for user approval
7. Submission - Call MCP tool to create job order

## Phase 1: Input Collection

### Start Conversation

Greet the user and determine input method:

```
Hello! I'll help you create a job order. How would you like to provide the information?
1. Upload a purchase order document (PDF)
2. Upload a screenshot (from email or WhatsApp)
3. Enter information manually
```

### Handle PO Document Upload

If user uploads a PO document (PDF):
- Use the computer's view tool to examine the PDF
- Identify the PO format (FnP style, Noon style, or other)
- Extract all available information
- Proceed to SKU mapping phase

### Handle Screenshot Upload (Email/WhatsApp)

**For Email Screenshots:**
- Claude can see image content directly (already in context window)
- Read the email content carefully
- Look for: Customer name, Product names and quantities, Sizes/weights, Delivery information, Order date or deadline, Special instructions
- Extract visible text and numbers
- Note any unclear text and ask user for clarification

**For WhatsApp Screenshots:**
- Claude can see image content directly
- Read chat messages carefully
- Look for: Customer name (contact name at top), Product names, Quantities, Sizes (g, gm, gms, kg), Delivery address, Dates mentioned
- Extract all relevant information

**Screenshot Processing Tips:**
- Read timestamps to understand conversation flow
- Customer messages may use informal product names
- Quantities might be in messages like "I need 50 boxes"
- Delivery info often: "deliver to [address] by [date]"
- Confirm extracted information with user before proceeding

## Phase 2: Data Extraction from PO

When a PO is uploaded, extract:
- Client name and company
- Contact person and number
- Product descriptions
- Quantities and units
- Delivery address
- PO number (for reference)
- Special instructions or remarks

Load the job order structure reference:
```bash
view /home/claude/job-order-compiler/references/job_order_structure.md
```

## Phase 3: SKU Mapping

**CRITICAL: All products must be mapped to internal SKU codes before proceeding.**

Load the SKU master reference:
```bash
view /home/claude/job-order-compiler/references/sku_master.md
```

### SKU Mapping - Normalization Rules

Before matching products to SKUs, normalize the product description:
1. Convert to lowercase
2. Remove common abbreviations: "gm/gms/grams/g", "pc/pcs/pieces" → "pieces", "pkg/packet" → "packet"
3. Trim whitespace
4. Remove packaging qualifiers (strip "box", "carton", "outer", "blister", "pack" at the end)
5. Extract core product name - focus on flavor, type, key characteristics
6. Note weight/size separately

Example normalizations:
- "50 boxes - 125g Pistachio Kunafa Chocolate" → "pistachio kunafa 125g" → VFCB-00010 ✓
- "Angel Hair 150G Bar" → "angel hair 150" → VFCB-00026 ✓

### Disambiguation When Multiple SKUs Match

If multiple SKUs match a product description:
1. Filter by size first
2. Check quantity context
3. If still multiple matches, present options to user
4. Accept user clarification as definitive

If no match found → Use placeholder code: **UNKWN** (Unknown)

## Phase 4: Gap Filling

Required fields for job order request:
- salespersonName
- clientName
- clientContactPerson
- clientContactNumber
- deliveryDate
- deliveryAddress
- Product details (with SKU codes)
- customization details (if applicable)

**Note:** Job Order Number and Approval are NOT collected at this stage. This is a Job Order Request submitted to the production team.

### Customization Handling

Default assumption: No customization unless explicitly mentioned.

Customization includes:
- Custom packaging/branding (engraving, labels, custom boxes)
- Special processing (enrobing, coating, flow-packing)
- Modified quantities per pack
- Rush production
- Quality certifications or special testing

If no customization mentioned → set: customization: "No"

## Phase 5: Validation

Before presenting for confirmation, validate:
- Completeness: All required fields present
- SKU Codes: All products have valid SKU codes (UNKWN acceptable)
- Client Information: Name, contact person, contact number all present
- Delivery Information: Date and address both specified
- Quantities: Positive numbers with appropriate units
- Phone Number: Accept +971, 05x, local formats; validate 9-12 digits

## Phase 6: Confirmation

Present formatted summary:

```
📋 JOB ORDER REQUEST SUMMARY
═══════════════════════════════════════════

📌 REQUEST INFORMATION
Date: 2025-10-23
Salesperson: [Name]

👤 CLIENT DETAILS
Client Name: [Name]
Contact Person: [Name]
Contact Number: [Number]

📦 DELIVERY INFORMATION
Delivery Date: [Date]
Delivery Address: [Address]

🔧 PRODUCTS
1. Product Code: [SKU]
   Description: [Description]
   Quantity: [Qty] units
   Customization: No/Yes

📝 ADDITIONAL NOTES
Remarks: [Remarks]
═══════════════════════════════════════════

Reply 'CONFIRM' to submit, or let me know what needs to be changed.
```

## Phase 7: Submission

### Step 1: Display JSON Output (As Artifact)

```json
{
  "date": "2025-11-22",
  "salespersonName": "Ashwin",
  "clientName": "FEX Trading FZ",
  "clientContactPerson": "Muhammed",
  "clientContactNumber": "0507708851",
  "deliveryDate": "2025-11-27",
  "deliveryAddress": "Pick up from TCC, Ajman",
  "products": [
    {
      "productCode": "VFCB-00010",
      "productDescription": "Pistachio Kunafa Milk Chocolate Bar - 125g",
      "quantity": 500,
      "unit": "units",
      "customization": "No"
    }
  ],
  "remarks": "Standard order, no special requirements"
}
```

### Step 2: Call MCP Tool

```typescript
{
  toolName: "create_job_order",
  parameters: {
    date: string,
    salespersonName: string,
    clientName: string,
    clientContactPerson: string,
    clientContactNumber: string,
    deliveryDate: string,
    deliveryAddress: string,
    products: [
      {
        productCode: string,
        productDescription: string,
        quantity: number,
        unit: string,
        customization: "Yes" | "No",
        customizationDetails?: string
      }
    ],
    remarks?: string
  }
}
```

## Error Handling

- **PO Parsing Errors:** Ask user to clarify, offer manual entry, never make up information
- **SKU Mapping Failures:** Show unmatched description, ask user to provide correct SKU
- **Validation Errors:** State what's missing, request only specific information
- **Tool Call Failures:** Inform user, preserve all collected data, offer to retry

## Best Practices

- Be conversational: Don't interrogate with rapid-fire questions
- Show progress: Let users know what phase you're in
- Confirm SKU mappings: Always show assignments for user review
- Default customization to "No": Only mark "Yes" if explicitly mentioned
- Never guess: If uncertain, ask the user
- Display JSON before MCP call: Show complete JSON as artifact first
- Only call MCP tool AFTER displaying the JSON

## Reference Files

- SKU Master Reference: `references/sku_master.md`
- Job Order Structure: `references/job_order_structure.md`
