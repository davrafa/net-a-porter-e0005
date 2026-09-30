# Data Mapping Documentation: Net-a-Porter (UK)

This repository contains the data mapping and extraction logic for the Net-a-Porter project, structured for the Data Boutique E0005 schema.

## 1. Project Specifications

*   **Data Source:** Net-a-Porter (https://www.net-a-porter.com/)
*   **Geography:** GBR (United Kingdom)
*   **Schema:** E0005
*   **Update Frequency:** Daily
*   **Currency:** GBP (£)

## 2. Data Mapping (E0005 Schema)

The extraction process maps standard e-commerce data points from the Net-a-Porter product pages to the final delivery file as follows:

*   `product_url`: The complete canonical URL of the product detail page.
*   `product_id`: The unique product identifier extracted from the URL or backend JSON.
*   `brand`: The designer or brand name of the item.
*   `product_name`: The complete title/designation of the product.
*   `regular_price`: The original retail price in GBP (excluding discounts).
*   `discount_price`: The current selling price in GBP (matches `regular_price` if no active promotion exists).
*   `currency`: Hardcoded as `GBP` to match the target geography.
*   `size`: **[Mandatory]** The specific size designation for the variant (e.g., "S", "M", "IT 40", "UK 8").
*   `quantity`: **[Mandatory]** The stock level available for that specific size. If exact stock quantities are hidden by the platform, this will map to boolean availability indicators (e.g., "In Stock" = 1, "Low Stock" = 1, "Out of Stock" = 0).

## 3. Variant Handling Logic

To comply with the mandatory requirement of delivering **one record per size with quantity**, the extraction script applies the following logic:

1.  The crawler accesses the base product page and reads the internal sizing array.
2.  The product is unnested: if an item has 4 available sizes, the output file will generate 4 distinct rows.
3.  Base data (URL, name, brand, price) is duplicated across these rows, while the `size` and `quantity` fields remain unique to accurately reflect the availability of each specific variant.
