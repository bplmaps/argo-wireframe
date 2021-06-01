<template>

  <Layout>

<div class="container mx-auto">
          <div v-if="collectionObjectMetadata">
    <h1>{{ collectionObjectMetadata.metadata[0].value }}</h1>
    <img :src="previewImage">

    <table>
      <tr v-for="metadataEntry, i in collectionObjectMetadata.metadata" v-bind:key="i">
        <td>{{ metadataEntry.label }}</td>
        <td>{{ metadataEntry.value }} </td>
      </tr>
    </table>
  </div>
</div>

  </Layout>

</template>

<script>
export default {
  data() {
    return {
      collectionObjectMetadata: null
    }
  },

  computed: {
    previewImage: function(){ 
      return this.collectionObjectMetadata.sequences[0].canvases[0].images[0].resource.service['@id'] + '/full/2000,/0/default.jpg';
    }
  },

  async mounted() {
    const { id } = this.$route.params
    const response = await fetch(`https://collections.leventhalmap.org/search/${id}/manifest.json`)
    this.collectionObjectMetadata = await response.json();

  }
}
</script>