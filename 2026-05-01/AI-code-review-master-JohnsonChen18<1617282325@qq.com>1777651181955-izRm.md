Review of the Code Changes in `OpenAiCodeReview.java`:

```diff
diff --git a/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java b/openai-code-review-sdk/src/main/java/plus/gaga/middleware/sdk/OpenAiCodeReview.java
index be6e6fa..163c704 100644
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

Changes:

1. **JSON Key Change**:
   - The key used in the JSON payload has been changed from `"content"` to `"text"`.
   - This change could be related to a change in the expected format for the API request. If the API now expects the message content under the `"text"` key instead of `"content"`, this change is appropriate.
   - If this change is intentional, it should be documented to ensure that all users of this SDK are aware of the new API requirement.

2. **Potential Documentation Update**:
   - Since the JSON key has been changed, it's important to update any documentation that refers to this key. This includes API usage guides, examples, and any automated documentation that the SDK generates.

3. **Code Review Recommendations**:
   - **Test Coverage**: Ensure that the code changes are covered by appropriate unit tests. If the API's expected payload key has changed, existing tests might fail and need to be updated.
   - **API Consistency**: Verify that this change is consistent across the SDK, if applicable. Other parts of the code that interact with this API endpoint should use the same key change.
   - **Code Review**: The code should be reviewed by another developer to confirm that this change is necessary and correctly implemented, especially if it's a public API or SDK.

Overall, the change appears to be minor but important for the correct operation of the SDK. It should be accompanied by a thorough testing cycle and updated documentation.