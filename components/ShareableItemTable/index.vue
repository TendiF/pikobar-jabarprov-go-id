<template>
  <div>
    <slot>
      <table class="w-full">
        <thead class="hidden sm:table-header-group">
          <tr class="bg-gray-200 border-b-2 border-t-2 border-solid border-gray-300">
            <th
              v-for="(col, index) in columns"
              :key="index"
              class="p-2 opacity-50 text-left"
            >
              {{ col.label }}
            </th>
            <th style="width: auto;" />
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, rowIndex) in items"
            :key="rowIndex"
            class="cursor-pointer hover:bg-gray-100 flex flex-col sm:table-row mb-6 sm:mb-0"
          >
            <td
              v-for="(col, colIndex) in columns"
              :key="colIndex"
              class="sm:p-2 text-left sm:border-b border-solid border-gray-300"
              @click.capture="onClickItem(item)"
            >
              {{ getCellValue(col, item, colIndex, rowIndex) }}
            </td>
            <td
              class="-mx-2 sm:mx-0 text-left sm:text-right sm:border-b border-solid border-gray-300"
            >
              <p class="flex-none text-sm text-gray-600">
                <button
                  class="px-2 py-2 mr-1 rounded-md hover:bg-gray-300"
                  @click="beforeDownload(item)"
                >
                  <FontAwesomeIcon :icon="icon.faDownload" class="mr-1" />
                  <span>
                    Unduh
                  </span>
                </button>
                <button
                  class="px-2 py-2 rounded-md hover:bg-gray-300"
                  @click="beforeShare(item)"
                >
                  <FontAwesomeIcon :icon="icon.faShare" class="mr-1" />
                  <span>
                    Bagikan
                  </span>
                </button>
              </p>
            </td>
          </tr>
        </tbody>
      </table>
    </slot>
  </div>
</template>

<script>
import _get from 'lodash/get'
import { faDownload, faShare } from '@fortawesome/free-solid-svg-icons'
import { onDownload, onShare } from '~/lib/download-and-share-firestore-doc'
export default {
  props: {
    columns: {
      type: Array,
      default: () => []
    },
    items: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      icon: {
        faDownload,
        faShare
      }
    }
  },
  methods: {
    getCellValue (column, row, columnIndex, rowIndex) {
      const format = column.format
      const value = _get(row, column.prop)
      return typeof format === 'function' ? format(value) : value
    },
    onClickItem (item) {
      this.$router.push({
        path: item.route
      })
    },
    beforeDownload (item) {
      onDownload(item.document_url, item.title)
    },
    beforeShare (item) {
      onShare(item.shareText)
    }
  }
}
</script>

<style>

</style>
