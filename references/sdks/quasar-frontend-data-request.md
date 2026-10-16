# Quasar frontend data request

[Context index](typescript-framework-workflows.md) · [Setup and preceding context](quasar-database-server-configuration.md)

Add this component logic to `IndexPage.vue` after configuring the [server endpoint](quasar-server-items-endpoint.md). The same-origin backend performs the SQL query; this Vue component has no database token or SDK instance.

```vue
<script setup lang="ts">
import { onMounted, ref } from "vue";

const items = ref<unknown[]>([]);
const errorMessage = ref("");
onMounted(async () => {
  try {
    const response = await fetch("/api/items", { credentials: "same-origin" });
    if (!response.ok) throw new Error(`Request failed: ${response.status}`);
    const data = await response.json();
    items.value = data.items;
  } catch (error) {
    errorMessage.value = error instanceof Error ? error.message : "Request failed";
  }
});
</script>

<template>
  <p v-if="errorMessage">{{ errorMessage }}</p>
  <pre v-else>{{ items }}</pre>
</template>
```
