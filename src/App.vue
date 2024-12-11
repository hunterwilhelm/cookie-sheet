<script setup lang="ts">
import { mdiArrowLeft, mdiArrowRight } from '@mdi/js'
import * as cookie from 'cookie'
import { computed, ref } from 'vue'

const text = ref(
  'session_id=abc123xyz; Path=/; Domain=example.com; Expires=Wed, 15 Jan 2025 12:00:00 GMT; Max-Age=2592000; HttpOnly; Secure; SameSite=Strict',
)
const selectedKeys = ref<string[]>([])
const cookieRows = ref<{ key: string; value: string | undefined }[]>([])
copyToRight()
const rows = computed(() => {
  return cookieRows.value
    .map((row) => ({
      ...row,
    }))
    .sort((a, b) => a.key.localeCompare(b.key))
})

function copyToRight() {
  cookieRows.value = Object.entries(cookie.parse(text.value)).map(([key, value]) => ({
    key,
    value,
  }))
  selectedKeys.value = cookieRows.value.map((row) => row.key)
}

function copyToLeft() {
  const selectedRows = rows.value.filter((row) => selectedKeys.value.includes(row.key))
  const serialized = selectedRows
    .map((row) => cookie.serialize(row.key, row.value ?? ''))
    .join('; ')
  text.value = serialized
}
</script>

<template>
  <VCardTitle>Cookie Sheet</VCardTitle>
  <VRow>
    <VCol cols="5">
      <VTextarea v-model="text" label="Paste your cookie here" />
    </VCol>
    <VCol cols="1">
      <VBtn @click="copyToLeft">
        <VIcon :icon="mdiArrowLeft"></VIcon>
      </VBtn>
      <VBtn @click="copyToRight">
        <VIcon :icon="mdiArrowRight"></VIcon>
      </VBtn>
    </VCol>
    <VCol cols="5">
      <VDataTable
        :items="rows"
        hide-default-footer
        :items-per-page="1000000"
        show-select
        item-value="key"
        v-model="selectedKeys"
      >
      </VDataTable>
    </VCol>
  </VRow>
</template>

<style scoped></style>
