<template>
  <v-card-text>
    <v-row>
      <v-col
        cols="12"
        sm="6"
        md="12"
        lg="6"
      >
        <app-named-slider
          :label="$t('app.general.label.retract_length')"
          suffix="mm"
          :value="retract_length"
          :reset-value="defaults.retract_length"
          :min="0"
          :max="retract_length_max"
          :step="0.01"
          overridable
          :disabled="!klippyReady"
          :locked="isMobileViewport"
          :loading="hasWait($waits.onSetRetractLength)"
          @submit="setRetractLength"
        />
      </v-col>
      <v-col
        cols="12"
        sm="6"
        md="12"
        lg="6"
      >
        <app-named-slider
          :label="$t('app.general.label.unretract_extra_length')"
          suffix="mm"
          :value="unretract_extra_length"
          :reset-value="defaults.unretract_extra_length"
          :min="0"
          :max="unretract_extra_length_max"
          :step="0.01"
          overridable
          :disabled="!klippyReady"
          :locked="isMobileViewport"
          :loading="hasWait($waits.onSetUnretractExtraLength)"
          @submit="setUnRetractExtraLength"
        />
      </v-col>
    </v-row>

    <v-row>
      <v-col
        cols="12"
        sm="6"
        md="12"
        lg="6"
      >
        <app-named-slider
          :label="$t('app.general.label.retract_speed')"
          suffix="mm/s"
          :value="retract_speed"
          :reset-value="defaults.retract_speed"
          :min="0"
          :step="1"
          :max="retract_speed_max"
          overridable
          :disabled="!klippyReady"
          :locked="isMobileViewport"
          :loading="hasWait($waits.onSetRetractSpeed)"
          @submit="setRetractSpeed"
        />
      </v-col>
      <v-col
        cols="12"
        sm="6"
        md="12"
        lg="6"
      >
        <app-named-slider
          :label="$t('app.general.label.unretract_speed')"
          suffix="mm/s"
          :value="unretract_speed"
          :reset-value="defaults.unretract_speed"
          :min="0"
          :step="1"
          :max="unretract_speed_max"
          overridable
          :disabled="!klippyReady"
          :locked="isMobileViewport"
          :loading="hasWait($waits.onSetUnretractSpeed)"
          @submit="setUnretractSpeed"
        />
      </v-col>
    </v-row>

    <v-row v-if="supportsZHopHeight">
      <v-col
        cols="12"
        sm="6"
        md="12"
        lg="6"
      >
        <app-named-slider
          :label="$t('app.general.label.z_hop_height')"
          suffix="mm"
          :value="z_hop_height"
          :reset-value="defaults.z_hop_height"
          :min="0"
          :step="0.1"
          :max="z_hop_height_max"
          overridable
          :disabled="!klippyReady"
          :locked="isMobileViewport"
          :loading="hasWait($waits.onSetZHopHeight)"
          @submit="setZHopHeight"
        />
      </v-col>
    </v-row>
  </v-card-text>
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator'
import StateMixin from '@/mixins/state'
import BrowserMixin from '@/mixins/browser'
import type { KlippyApp } from '@/store/printer/types'

@Component({})
export default class Retract extends Mixins(StateMixin, BrowserMixin) {
  get retract_length () {
    return this.$store.state.printer.printer.firmware_retraction.retract_length
  }

  get retract_length_max () {
    if (this.defaults.retract_length <= 0) return 15
    return Math.round(this.defaults.retract_length * 2 * 100) / 100
  }

  get retract_speed () {
    return this.$store.state.printer.printer.firmware_retraction.retract_speed
  }

  get retract_speed_max () {
    if (this.defaults.retract_speed <= 0) return 100
    return Math.round(this.defaults.retract_speed * 2)
  }

  get unretract_speed () {
    return this.$store.state.printer.printer.firmware_retraction.unretract_speed
  }

  get unretract_speed_max () {
    if (this.defaults.unretract_speed <= 0) return 100
    return Math.round(this.defaults.unretract_speed * 2)
  }

  get unretract_extra_length () {
    return this.$store.state.printer.printer.firmware_retraction.unretract_extra_length
  }

  get unretract_extra_length_max () {
    if (this.defaults.unretract_extra_length <= 0) return 15
    return Math.round(this.defaults.unretract_extra_length * 2 * 100) / 100
  }

  get z_hop_height () {
    return this.$store.state.printer.printer.firmware_retraction.z_hop_height
  }

  get z_hop_height_max () {
    if (this.defaults.z_hop_height <= 0) return 2
    return Math.round(this.defaults.z_hop_height * 2 * 100) / 100
  }

  get defaults () {
    return this.$store.getters['printer/getPrinterSettings']('firmware_retraction') || {}
  }

  get klippyApp (): KlippyApp {
    return this.$store.getters['printer/getKlippyApp'] as KlippyApp
  }

  get supportsZHopHeight () {
    return this.klippyApp.isKalicoOrDangerKlipper
  }

  setRetractLength (val: number) {
    this.sendGcode(`SET_RETRACTION RETRACT_LENGTH=${val}`, this.$waits.onSetRetractLength)
  }

  setRetractSpeed (val: number) {
    this.sendGcode(`SET_RETRACTION RETRACT_SPEED=${val}`, this.$waits.onSetRetractSpeed)
  }

  setUnretractSpeed (val: number) {
    this.sendGcode(`SET_RETRACTION UNRETRACT_SPEED=${val}`, this.$waits.onSetUnretractSpeed)
  }

  setUnRetractExtraLength (val: number) {
    this.sendGcode(`SET_RETRACTION UNRETRACT_EXTRA_LENGTH=${val}`, this.$waits.onSetUnretractExtraLength)
  }

  setZHopHeight (val: number) {
    this.sendGcode(`SET_RETRACTION Z_HOP_HEIGHT=${val}`, this.$waits.onSetZHopHeight)
  }
}
</script>
