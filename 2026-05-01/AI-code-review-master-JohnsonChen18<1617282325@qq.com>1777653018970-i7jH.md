Review of the `OpenAiCodeReview.java` code diff:

```plaintext
diff --git a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java
index 2e9c2f1..b09bb54 100644
--- a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java
+++ b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java
@@ -192,7 +192,7 @@ public class OpenAiCodeReview {
         connection.setRequestProperty("Content-Type", "application/json");
         connection.setDoOutput(true);
 
-        String jsonPayload = String.format("{\"content\": \"%s\"}", message);
+        String jsonPayload = String.format("{\"text\": \"%s\"}", message);
 
         try (OutputStream os = connection.getOutputStream()) {
             byte[] input = jsonPayload.getBytes(StandardCharsets.UTF_8);
```

**Analysis:**

1. **Property Key Change:**
   - The key from `"content"` to `"text"` is a notable change. It's important to understand the rationale behind this change. If this is a change in the API's expected input, it should be clearly documented. If it's a mistake, it should be corrected back to `"content"`.

2. **Code Readability:**
   - The use of `String.format` is a simple and effective way to construct the JSON payload. However, it might be more readable to use a JSON library or a JSON builder to construct the payload. This would also reduce the risk of typos and make the code more maintainable.

3. **Error Handling:**
   - The code snippet provided does not show any error handling for the `OutputStream` operation. It is important to handle potential exceptions that could occur during the output stream operation to ensure the application's robustness.

4. **Commenting:**
   - There are no comments explaining the purpose of the `jsonPayload` variable or the reason for the change in the property key. Adding comments to explain such changes would be beneficial for future maintainers.

5. **API Consistency:**
   - If this change is intended to be consistent with the OpenAI API's expected input, it should be verified that this is the correct change. If it's a local change, it should be documented as such.

**Recommendations:**

- Verify the correctness of the property key change (`"content"` to `"text"`).
- Consider using a JSON library or a JSON builder for constructing the payload for better readability and maintainability.
- Add error handling for the `OutputStream` operation.
- Add comments to explain the purpose of the `jsonPayload` variable and the rationale behind the property key change.
- Ensure that the API usage is consistent with the OpenAI API's requirements or document any deviations.