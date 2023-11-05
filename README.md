Vulnerabilities
diff --git a/mono/utils/strtod.c b/mono/utils/strtod.c
index 1ba4b72..ddbdd17 100644
--- a/mono/utils/strtod.c
+++ b/mono/utils/strtod.c
@@ -542,7 +542,7 @@ Balloc
 #endif
 
 	ACQUIRE_DTOA_LOCK(0);
-	if ((rv = freelist[k])) {
+	if (k <= Kmax && (rv = freelist[k])) {
 		freelist[k] = rv->next;
 		}
 	else {
@@ -579,10 +579,14 @@ Bfree
 	free (v);
 #else
 	if (v) {
-		ACQUIRE_DTOA_LOCK(0);
-		v->next = freelist[v->k];
-		freelist[v->k] = v;
-		FREE_DTOA_LOCK(0);
+		if (v->k > Kmax)
+			free((void*)v);
+		else {
+			ACQUIRE_DTOA_LOCK(0);
+			v->next = freelist[v->k];
+			freelist[v->k] = v;
+			FREE_DTOA_LOCK(0);
+		}
 	}
 #endif
 }
@ehdb96
Comment
