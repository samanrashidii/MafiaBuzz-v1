<template>
  <Overlay class="log-history">
    <template v-for="(history, index) in dashboard.totalHistory">
      <div
        class="log-table"
        :key="index"
        v-if="history.length > 0"
      >
        <span class="counter">{{ $t('common.Night') }} {{ index + 1 }}</span>
        <table>
          <tr
            v-for="(log, index) in history"
            :key="index"
          >
            <td>{{ index+1 }}</td>
            <td>
              <img
                :src="getImg('/actions', log.image)"
                :alt="$t('god.actionIconAlt')"
              >
            </td>
            <td>
              <p v-html="log.text" />
            </td>
          </tr>
        </table>
      </div>
    </template>
    <div v-if="dashboard.totalHistory.length === 0">
      <h2>{{ $t('god.noLog') }}</h2>
    </div>
    <AppButton
      @click.native="closeLog()"
      class="active has-small-top-margin"
    >
      <span>{{ $t('god.logCloseButton') }}</span>
    </AppButton>
  </Overlay>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import Overlay from '@/components/Overlay.vue';

export default {
  components: {
    Overlay,
  },
  computed: {
    ...mapGetters({
      Dashboard: 'dashboard/Dashboard',
    }),
    dashboard() {
      return JSON.parse(JSON.stringify(this.Dashboard));
    },
  },
  methods: {
    closeLog() {
      this.$emit('closeLog', false);
    },
  },
};
</script>
