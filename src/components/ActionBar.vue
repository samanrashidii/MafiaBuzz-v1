<template>
  <div
    class="priority-box"
  >
    <!-- Before Action Box -->
    <transition name="fade">
      <div
        class="before-action-box"
        v-if="dashboard.mafiaParty && dashboard.round === 1"
      >
        <div class="table-display">
          <div class="table-cell-display">
            <img
              src="@/assets/images/roles/mafiaparty.svg"
              :alt="$t('god.mafiaPartyIconAlt')"
            >
            <p class="site-color">
              {{ $t('god.mafiaPartyText') }}
            </p>
            <ul class="error-bullet type-2">
              <li
                v-for="(mp, index) in $t('god.mafiaPartyException')"
                :key="index"
                v-html="mp"
              />
            </ul>
            <AppButton
              class="has-small-top-margin"
              @click.native="mafiaPartyController()"
            >
              {{ $t('god.mafiaPartyButton') }}
            </AppButton>
          </div>
        </div>
      </div>
    </transition>

    <!-- Next Killer Box -->
    <transition name="fade">
      <NextKillerBox v-if="dashboard.killerChanged" />
    </transition>

    <!-- Role Viewer -->
    <Overlay :class="{'active': gameSettings.roleViewer, 'viewer': true}">
      <RoleViewer
        v-if="gameSettings.roleViewer"
        :roles="gameSettings.viewerItems"
        :show="gameSettings.roleViewer"
      />
    </Overlay>

    <!-- Actions Progress Bar -->
    <div class="progress-bar">
      <a
        class="prev-action"
        href="javascript:void(0)"
        @click="navigateActions(dashboard.actionProgress, 'prev')"
        v-if="dashboard.actionProgress !== 0"
      />
      <div class="bar-holder">
        <span :style="{width: progress+'%'}" />
        <i><strong>{{ dashboard.actionProgress }}</strong> / {{ dashboard.actionBox.length }}</i>
      </div>
      <a
        class="next-action"
        href="javascript:void(0)"
        @click="navigateActions(dashboard.actionProgress, 'next')"
        v-if="dashboard.actionProgress !== dashboard.actionBox.length"
      />
    </div>

    <!-- Handle Actions -->
    <div>
      <div
        class="action-box-wrapper"
        v-for="(player, index) in dashboard.actionBox"
        :key="index"
      >
        <!-- Actions Overlay -->
        <template v-if="dashboard.actionProgress === index">
          <div
            class="action-overlay hacked-overlay"
            v-if="checkStatus(player).status.hacked"
            key="hackedTarget"
          >
            <div class="table-display">
              <div class="table-cell-display">
                <img
                  src="@/assets/images/roles/hacked.svg"
                  :alt="$t('god.hackedIconAlt')"
                >
                <p><span>{{ $t(player.name) }} </span> <strong v-html="$t('god.hackedPerson')" /></p>
                <AppButton
                  class="purple"
                  @click.native="skipAction(index)"
                >
                  {{ $t('god.skipButton3') }}
                </AppButton>
              </div>
            </div>
          </div>
          <div
            class="action-overlay dead-overlay"
            v-if="checkStatus(player).status.dead"
            key="deadTarget"
          >
            <div class="table-display">
              <div class="table-cell-display">
                <div class="has-clear-fix">
                  <img
                    src="@/assets/images/roles/grave.svg"
                    :alt="$t('god.deadIconAlt')"
                  >
                  <img
                    class="overlap"
                    :src="getImg('/roles', player.icon)"
                    :alt="$t('god.playerIconAlt')"
                  >
                </div>
                <p><span>{{ $t(player.name) }} </span> <strong v-html="$t('god.deadPerson')" /></p>
                <AppButton
                  class="black"
                  @click.native="skipAction(index)"
                >
                  {{ $t('god.skipButton3') }}
                </AppButton>
              </div>
            </div>
          </div>

          <p>{{ $t('god.actionQuestion1') }}<span :class="{'mafia-role': player.mafia, 'citizen-role': !player.mafia && !player.solo , 'solo-role': !player.mafia && player.solo}"> {{ $t(player.name) }} </span> {{ $t('god.actionQuestion2') }} <strong>{{ $t(player.action.action) }}</strong> ?</p>
          <ActionBox
            :player="player"
            :player-index="index"
          />
        </template>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
import ActionBox from '@/components/ActionBox.vue';
import NextKillerBox from '@/components/NextKillerBox.vue';
import Overlay from '@/components/Overlay.vue';
import RoleViewer from '@/components/RoleViewer.vue';
import actionLog from '@/mixins/dashboard/actionLog';
import actions from '@/mixins/dashboard/actions';
import actionFilters from '@/mixins/dashboard/actionFilters';
import beforeActions from '@/mixins/dashboard/beforeActions';
import deadWatcher from '@/mixins/dashboard/deadWatcher';
import nextAction from '@/mixins/dashboard/nextAction';
import navigateActions from '@/mixins/dashboard/navigateActions';
import passiveActive from '@/mixins/dashboard/passiveActive';
import saveHistory from '@/mixins/dashboard/saveHistory';
import skipAction from '@/mixins/dashboard/skipAction';
import voteKiller from '@/mixins/dashboard/voteKiller';
import trackingStatus from '@/mixins/dashboard/trackingStatus';

export default {
  components: {
    ActionBox,
    NextKillerBox,
    Overlay,
    RoleViewer,
  },
  computed: {
    ...mapGetters({
      Dashboard: 'dashboard/Dashboard',
      GameSettings: 'gameStatus/GameSettings',
      ReplacingRoles: 'roles/ReplacingRoles',
      DefaultState: 'DefaultState',
    }),
    gameSettings() {
      return JSON.parse(JSON.stringify(this.GameSettings));
    },
    dashboard() {
      return JSON.parse(JSON.stringify(this.Dashboard));
    },
    replacingRoles() {
      return JSON.parse(JSON.stringify(this.ReplacingRoles));
    },
    progress() {
      return (this.dashboard.actionProgress / this.dashboard.actionBox.length) * 100;
    },
  },
  methods: {
    ...mapActions({
      SetDashboard: 'dashboard/SetDashboard',
      SetGameSettings: 'gameStatus/SetGameSettings',
      SetReplacingRoles: 'roles/SetReplacingRoles',
    }),
    checkStatus(player) {
      let target;
      if (player) {
        this.gameSettings.selectedRoles.forEach((element) => {
          if (element.player === player.player) {
            target = element;
          }
        });
      }
      return target;
    },
  },
  mixins: [
    actionLog,
    actions,
    actionFilters,
    beforeActions,
    deadWatcher,
    navigateActions,
    nextAction,
    passiveActive,
    saveHistory,
    skipAction,
    voteKiller,
    trackingStatus,
  ],
};
</script>
