<template>
  <div class="discord-box has-xsmall-bottom-margin">
      <PageBox class="dark-color">
        <img class="default-size has-xsmall-bottom-margin" src="@/assets/images/icons/discord.svg" alt="Discord Icon" />
        <template v-if="!this.GameSettings.discordChannel">
          <p
            v-html="$t('thirdparty.discordText')"
            class="center-aligned"
          />
          <form
            class="small-size"
            @submit.prevent="connectDiscord()"
          >
            <div class="field-holder">
              <input
                v-model="connectKey"
                placeholder="https://discord.com/api/webhooks/693373413607211028/xe8kyVred3V-QFhbp1OvP6tLNu8a5O7m9fuLYra62nK2R6ZYPO6ZzClWgj4QR73YG7O2"
                class="ltr center-aligned"
                type="text"
              />
            </div>
            <button
              type="submit"
              class="purple-bg sm has-minus-top-margin curve-rounded"
            >
              {{ $t('thirdparty.discordCodeSubmit') }}
            </button>
            <AppButton
              v-if="!this.GameSettings.discordChannel"
              href="https://www.instagram.com/s/aGlnaGxpZ2h0OjE4MTAxMDQ4NTMwMTM2MjIz?igshid=2pvecoho35pg"
              target="_blank"
              class="awesome sm-fontsize"
            >
              {{ $t('thirdparty.discordHelp') }}
            </AppButton>
          </form>
        </template>
        <template v-else>
            <img class="onlayer-image" src="@/assets/images/assets/correct.svg" alt="Correct Icon" />
            <p class="has-small-top-margin center-aligned">{{ $t('thirdparty.discordConnected') }}</p>
            <AppButton
              class="danger"
              @click.native="disconnectDiscord()"
            >
              {{ $t('thirdparty.discordDisconnect') }}
            </AppButton>
        </template>
      </PageBox>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex';
export default {
  data () {
    return {
        connectKey: null
    }
  },
  computed: {
    ...mapGetters({
      GameSettings: 'gameStatus/GameSettings',
    })
  },
  methods: {
    ...mapActions({
      SetDiscordChannel: 'gameStatus/SetDiscordChannel'
    }),
    connectDiscord () {
      const isWebhookCode = this.connectKey.indexOf('discord.com/api/webhooks')
      if (isWebhookCode !== -1) {
        setTimeout(() => {
          this.SetDiscordChannel(this.connectKey)
        }, 700)
      }
    },
    disconnectDiscord () {
      setTimeout(() => {
        this.SetDiscordChannel(false)
      }, 700)
    }
  }
}
</script>
