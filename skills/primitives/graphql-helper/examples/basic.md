# Example — read and write a product metafield

There is no direct tool for metafields. Skills use the GraphQL helper instead.

## Read a single metafield

```
Caller: catalog.metafield-audit (hypothetical)
Step:   read product.custom.care_instructions

1. graphql_schema('Product')
   → confirms `metafield(namespace: String!, key: String!): Metafield` exists.

2. (skip docs — schema is enough)

3. validate_graphql_codeblocks([{
     content: "
       query GetCareInstructions($id: ID!) {
         product(id: $id) {
           id
           metafield(namespace: \"custom\", key: \"care_instructions\") {
             id
             value
             type
           }
         }
       }
     "
   }])
   → ok

4. graphql_query(operation, variables: { id: "gid://shopify/Product/000" })
   → { product: { id: "...", metafield: { id: "...", value: "Hand wash cold", type: "single_line_text_field" } } }

5. Return to caller.
```

## Write a metafield

```
Caller: catalog.metafield-audit
Step:   set product.custom.care_instructions = "Machine wash warm"

1. graphql_schema('Mutation')
   → grep for "metafields" → metafieldsSet(metafields: [MetafieldsSetInput!]!): MetafieldsSetPayload

2. graphql_schema('MetafieldsSetInput')
   → reveals required fields: ownerId, namespace, key, type, value

3. validate_graphql_codeblocks([{
     content: "
       mutation SetCareInstructions($input: [MetafieldsSetInput!]!) {
         metafieldsSet(metafields: $input) {
           metafields { id key namespace value }
           userErrors { field message }
         }
       }
     "
   }])
   → ok

4. The host prompts the merchant for confirmation (mutation).
5. graphql_mutation(operation, variables: { input: [{ ownerId: "gid://shopify/Product/000", namespace: "custom", key: "care_instructions", type: "single_line_text_field", value: "Machine wash warm" }] })
6. Return result. Caller surfaces to merchant: "Updated care instructions on Product A."
```

If validation in step 3 had returned an error like `Field 'metafield' doesn't exist on MetafieldsSetPayload`, the helper would NOT proceed to execute — it would return the validation error and the caller would correct the operation.
