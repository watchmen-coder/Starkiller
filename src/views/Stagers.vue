<template>
  <div>
    <v-breadcrumbs :items="breads" />

    <div class="headers">
      <h3>Stagers</h3>
      <v-btn
        color="primary"
        rounded
        @click="create"
      >
        Generate Stager
      </v-btn>
    </div>
    <v-data-table
      :headers="headers"
      :items="stagers"
    >
      <template v-slot:item.createdAt="{ item }">
        <v-tooltip top>
          <template v-slot:activator="{ on }">
            <span v-on="on">{{ moment(item.createdAt).fromNow() }}</span>
          </template>
          <span>{{ moment(item.createdAt).format('lll') }}</span>
        </v-tooltip>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
          v-if="isDownload(item)"
          style="padding-right: 5px"
          small
          @click="download(item)"
        >
          fa-download
        </v-icon>
        <v-icon
          v-else
          style="padding-right: 5px"
          small
          @click="copy(item)"
        >
          fa-paperclip
        </v-icon>
        <v-icon
          small
          @click="deleteStager(item)"
        >
          fa-trash-alt
        </v-icon>
      </template>
    </v-data-table>
  </div>
</template>

<script>
import { namespacedElectronStore as electronStore } from '@/store/electron-store';
import DownloadMixin from '@/mixins/download-stager';
import CopyMixin from '@/mixins/copy-stager';
import moment from 'moment';

export default {
  name: 'Stagers',
  components: {
  },
  mixins: [DownloadMixin, CopyMixin],
  data() {
    return {
      moment,
      breads: [
        {
          text: 'Stagers',
          disabled: true,
          href: '/stagers',
        },
      ],
      headers: [
        { text: 'Name', value: 'name' },
        { text: 'Listener', value: 'Listener.Value' },
        { text: 'Language', value: 'Language.Value' },
        { text: 'SafeChecks', value: 'SafeChecks.Value' },
        { text: 'Created At', value: 'createdAt' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      stagers: [],
    };
  },
  mounted() {
    this.getStagers();
  },
  methods: {
    create() {
      this.$router.push({ name: 'stagerNew' });
    },
    async deleteStager(index) {
      if (await this.$root.$confirm('Delete', 'Are you sure you want to delete this stager?', { color: 'red' })) {
        this.stagers.splice(index, 1);
        electronStore.set('generatedStagers', this.stagers);
      }
    },
    isDownload(stager) {
      return stager.OutFile && stager.OutFile.Value.length > 0;
    },
    async copy(stager) {
      return this.copyStager(stager.Output);
    },
    async download(stager) {
      return this.downloadStager(stager.Output, stager.OutFile.Value);
    },
    getStagers() {
      this.stagers = electronStore.get('generatedStagers');
    },
  },
};
</script>

<style>

</style>
