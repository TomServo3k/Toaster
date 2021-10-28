<template>
  <div>

    <v-snackbar v-if="toastLocation === topLeft" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" top left > <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" color="#eeeeee" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else-if="toastLocation === topCenter" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" top center >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else-if="toastLocation === topRight" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" top right >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else-if="toastLocation === center" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" top center :style="centerScreen" >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-show="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else-if="toastLocation === bottomLeft" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" bottom left >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else-if="toastLocation === bottomCenter" class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" bottom center >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-snackbar v-else class="snackbar" v-model="showToaster" :timeout="toasterTimeout" :color="toastColor" bottom right >
      <span v-html="toastText"></span>
      <span v-if="canCountdown" class="countdown-span">{{ countDown }}</span>
      <span v-if="canClose" class="close-span">
        <v-icon :style="closeIcon" @click="closeToast()">clear</v-icon>
      </span>
      <v-btn v-if="showClose" right class="close-button px-2" @click="closeToast()">Close</v-btn>
    </v-snackbar>

    <v-overlay :value="showOverlay">
    </v-overlay>

    <v-dialog
      v-model="showConfirm"
      persistent
      max-width="500px"
      transition="dialog-transition"
    >
      <template></template>
      <v-card class="noscroll zContainer">
        <v-card-title :class="headerColor">
          {{ confirmTitle }}
        </v-card-title>
        <v-divider></v-divider>
        <v-row class="px-2 py-2">
          <v-col cols="1"></v-col>
          <v-col v-html="confirmMessage" cols="10"></v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" class="text-center">
            <v-divider></v-divider>
            <v-btn dense class="mx-2 my-2" color="primary" @click="confirmYes" >{{yesPrompt}}</v-btn>
            <v-btn v-if="noPrompt" dense class="mx-2" color="gray" @click="confirmNo" >{{noPrompt}}</v-btn>
          </v-col>
        </v-row>
      </v-card>
    </v-dialog>

    <v-dialog
      v-model="showAlert"
      persistent
      max-width="500px"
      transition="dialog-transition"
    >
      <template></template>
      <v-card class="noscroll zAlert">
        <v-card-title :class="headerColor">
          {{ alertTitle }}
        </v-card-title>
        <v-divider></v-divider>
        <v-row class="px-2 py-2">
          <v-col cols="1"></v-col>
          <v-col v-html="alertMessage" cols="10"></v-col>
        </v-row>
        <v-row dense>
          <v-col cols="12" class="text-center">
            <v-divider></v-divider>
            <v-btn dense class="mx-2 my-2" color="primary" align="center" @click="alertOk" >{{okPrompt}}</v-btn>
          </v-col>
        </v-row>
      </v-card>
    </v-dialog>

    <div v-if="anyNotifications" class="note-icon" :style="iconOffset">
      <v-dialog
        v-model="showNotifications"
        transition="dialog-transition"
      >
        <template v-slot:activator="{on}">
          <v-icon color="brown" v-on="on" @click="setOpenNotifications(true)">breakfast_dining</v-icon>
        </template>
        <v-card class="zContainer notification-window">
          <div class="vscroll py-2">
            <v-row dense v-for="note in filteredNotifications">
              <v-col cols="12" class="top-border">
                <v-row dense>
                  <v-col cols="11" style="color: brown">
                    <span style="text-transform: capitalize">{{typeDescriptions[note.params.type]}}</span> toasted at {{formatDate(note.date)}}
                  </v-col>
                  <v-col cols="1" class="right">
                    <v-tooltip bottom>
                      <template v-slot:activator="{on}">
                        <v-icon :style="closeIcon" color="red" @click="deleteNote(note)" v-on="on">delete</v-icon>
                      </template>
                      <span>Remove this notification</span>
                    </v-tooltip>
                  </v-col>
                  
                </v-row>
                <v-row dense style="margin-top: -.7rem;">
                  <v-col cols="12">
                    <span class="text-wrap">{{note.params.message}}</span>
                  </v-col>
                </v-row>
              </v-col>
            </v-row>
          </div>
          <v-row dense class="top-border">
            <v-col cols="12">
              <v-row>
                <v-col cols="12" align="center" class="note-title">
                  <span><b>Toast Notifications</b></span>
                </v-col>
                <v-col cols="12" align="center">
                  <v-btn dense class="close-notes" color="primary" @click="clearNotificationList()" >Clear All</v-btn>
                  <v-btn dense class="close-notes" color="primary" @click="setOpenNotifications(false)" >Close</v-btn>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-card>
      </v-dialog>
    </div>

  </div>
</template>

<script lang="ts">

  import { Component, Prop, Watch, Vue } from 'vue-property-decorator';

  const indefiniteToast = 0; // with vuetify version > 2.3 we'll need to change this to -1
  const aDay: number = 86400000;


  @Component
  export default class Toaster extends Vue {

    @Prop({ required: false, default: function () { return {}; } }) private options!: any;

    // local event bus

    private static EventBus = new Vue();

    // toast message types

    private static readonly toastInfo: string = 'toastInfo';
    private static readonly toastWarn: string = 'toastWarn';
    private static readonly toastError: string = 'toastError';
    private static readonly toastConfirm: string = 'toastConfirm';
    private static readonly toastAlert: string = 'toastAlert';
    private static readonly closeToaster: string = 'closeToaster';
    private static readonly closeConfirmDialog: string = 'closeConfirm';
    private static readonly closeAlertDialog: string = 'closeAlert';
    private static readonly showNotificationDialog: string = 'showNotificationList';
    private static readonly clearNotificationList: string = 'clearNotificationList';

    // constants for Vue template

    private readonly topLeft: number = Toaster.TopLeft;
    private readonly topCenter: number = Toaster.TopCenter;
    private readonly topRight: number = Toaster.TopRight;
    private readonly center: number = Toaster.Center;
    private readonly bottomLeft: number = Toaster.BottomLeft;
    private readonly bottomCenter: number = Toaster.BottomCenter;
    private readonly bottomRight: number = Toaster.BottomRight;

    // control variables for Vue
    
    // toast popups
    private toastText: string = '';
    private showToaster: boolean = false;
    private showOverlay: boolean = false;
    private toastTimeout: number = this.toasterOptions.timeout;
    private toastColor: string = this.toasterOptions.infoColor;
    private toastLocation: number = this.toasterOptions.location

    // confirm dialog
    private showConfirm: boolean = false;
    private confirmTitle: string = 'Please confirm';
    private confirmMessage: string = 'Do you want to continue?';
    private yesPrompt: string = 'Yes';
    private noPrompt: string = 'No';
    private yesCallback: Function = () => { }
    private noCallback: Function = () => { }

    // alert dialog
    private showAlert: boolean = false;
    private alertTitle: string = '';
    private alertMessage: string = 'Press OK to continue';
    private alertCallback: Function = () => { }
    private okPrompt: string = 'Yes';
    private countDownTime: number = 0;

    // for confirm and alert messages
    private headerColor: string = this.toasterOptions.headerColor;

    private static notifications: any[] = [];
    private anyNotifications: boolean = false;
    private showNotifications: boolean = false;

    // alternate timeout values
    public static readonly CantClose: number = -1;    // user is not allowed to close the toast
    public static readonly WaitForClose: number = 0;  // toast will not timeout and a Close button is displayed

    // locations for toast popups
    public static readonly TopLeft: number = 1;
    public static readonly TopCenter: number = 2;
    public static readonly TopRight: number = 3;
    public static readonly Center: number = 4;
    public static readonly BottomLeft: number = 5;
    public static readonly BottomCenter: number = 6;
    public static readonly BottomRight: number = 7;

    // public functions

    public static info(message: string, timeout: number = 4000, location: number | undefined = undefined ): void {
      Toaster.EventBus.$emit(Toaster.toastInfo, { message: message, color: 'infoColor', timeout: timeout, location: location, type: 'info' });
    }

    public static warn(message: string, timeout: number = 4000, location: number | undefined = undefined ): void {
      Toaster.EventBus.$emit(Toaster.toastWarn, { message: message, color: 'warnColor', timeout: timeout, location: location, type: 'warn' });
    }

    public static error(message: string, timeout: number = 4000, location: number | undefined = undefined ): void {
      Toaster.EventBus.$emit(Toaster.toastError, { message: message, color: 'errorColor', timeout: timeout, location: location, type: 'error' });
    }

    public static closeToast(): void {
      Toaster.EventBus.$emit(Toaster.closeToaster);
    }

    public static confirm(message: string, yesCallback: Function|null = null, noCallback: Function|null = null, yesPrompt: string|null = 'Yes', noPrompt: string|null = 'No', title: string|null = 'Please confirm', color: string|null = null): void {
      Toaster.EventBus.$emit(Toaster.toastConfirm, { message: message, yesCallback: yesCallback, noCallback: noCallback, yesPrompt: yesPrompt, noPrompt: noPrompt, title: title, color: color });
    }

    public static closeConfirm(answerYes: boolean): void {
      Toaster.EventBus.$emit(Toaster.closeConfirmDialog, { answerYes: answerYes });
    }

    public static alert(message: string, title: string|null = null, callback: Function|null = null, okPrompt: string|null = 'OK', color: string|null = null): void {
      Toaster.EventBus.$emit(Toaster.toastAlert, { message: message, okPrompt: okPrompt, title: title, callback: callback, color: color });
    }

    public static closeAlert(): void {
      Toaster.EventBus.$emit(Toaster.closeAlertDialog);
    }

    public static showNotificationList(): void {
      Toaster.EventBus.$emit(Toaster.showNotificationDialog);
    }

    public static clearNotifications(): void {
      Toaster.EventBus.$emit(Toaster.clearNotificationList);
    }

    // private functions

    private getColor(name: string): string {
      let color = '';
      switch (name) {
        case 'infoColor':
          color = this.toasterOptions.infoColor;
          break;
        case 'warnColor':
          color = this.toasterOptions.warnColor;
          break;
        case 'errorColor':
          color = this.toasterOptions.errorColor;
          break;
        case undefined:
          color = this.toasterOptions.infoColor;
          break;
        default:
          color = name;
          break;
      }
      return color;
    }

    private typeDescriptions: {} = {
      'info': 'Information',
      'warn': 'Warning'
    }

    private toast(params: any): void {
      this.toastColor = this.getColor(params.color);
      this.toastText = params.message || '(no message)';
      this.toastTimeout = Number(params.timeout) <= 0 ? Number(params.timeout) : ( Number(params.timeout) || 4000 );
      this.toastLocation = params.location || this.bottomRight;
      this.countDownTime = this.toastTimeout / 1000;
      this.showOverlay = this.toastTimeout <= 0;
      this.showToaster = true;
      if (this.toasterTimeout > 0) this.doCountDown();
      if (this.toasterOptions.hideAllNotifications) return;
      if (params.type === 'info' && this.toasterOptions.hideInfoNotifications) return;
      if (params.type === 'warn' && this.toasterOptions.hideWarnNotifications) return;
      Toaster.notifications.push({date: new Date(), params: params});
      this.anyNotifications = true;
    }

    private closeToast(): void {
      this.showToaster = false;
      this.showOverlay = false;
    }

    private confirm(params: any): void {
      this.confirmMessage = params.message || 'Do you want to continue?';
      this.yesCallback = params.yesCallback || (() => { });
      this.noCallback = params.noCallback || (() => { });
      this.yesPrompt = params.yesPrompt || 'Yes';
      this.noPrompt = params.noPrompt || 'No';
      this.confirmTitle = params.title || 'Please confirm';
      this.headerColor = params.color || this.toasterOptions.headerColor;
      this.showConfirm = true;
    }

    private confirmYes(): void {
      this.showConfirm = false;
      this.yesCallback();
    }

    private confirmNo(): void {
      this.showConfirm = false;
      this.noCallback();
    }

    private closeConfirmDialog(params: any): void {
      const answerYes = params.answerYes || false;
      if (answerYes)
        this.confirmYes();
      else
        this.confirmNo();
    }

    private alert(params: any): void {
      this.alertMessage = params.message || 'Press OK to continue';
      this.alertCallback = params.callback || (() => { });
      this.okPrompt = params.okPrompt || 'OK';
      this.alertTitle = params.title || 'Alert!';
      this.headerColor = params.color || this.toasterOptions.headerColor;
      this.showAlert = true;
    }

    private alertOk(): void {
      this.showAlert = false;
      this.alertCallback();
    }

    private closeAlertDialog(): void {
      this.alertOk();
    }

    private showNotificationDialog(): void {
      this.setOpenNotifications(true);
    }

    private clearNotificationList(): void {
      while (Toaster.notifications.length > 0)
        this.deleteNote(Toaster.notifications[0]);
    }

    private setOpenNotifications(choice: boolean): void {
      this.showNotifications = choice;
      //this.$forceUpdate();
    }

    private doCountDown(): void {
      if (!this.showToaster) this.countDownTime = 0;
      if (this.countDownTime >= 1) {
        this.countDownTime -= 1;
        setTimeout(this.doCountDown, 1000);
      } else {
        this.closeToast();
      }
    }

    private deleteNote(note: any): void {
      const index = Toaster.notifications.findIndex(n => n === note);
      Toaster.notifications.splice(index, 1);
      if (Toaster.notifications.length === 0) {
        this.setOpenNotifications(false);
        this.anyNotifications = false;
      }
      this.$forceUpdate();
    }

    private formatDate(date: Date): string {
      let a = 'AM';
      let h = 0;
      if (date.getHours() === 0) {
        h = 12;
      } else if (date.getHours() >= 12) {
        a = 'PM';
        h = date.getHours() - 12;
      }
      const hh = this.leadingZeros(h, 2);
      const mm = this.leadingZeros(date.getMinutes(), 2);
      const ss = this.leadingZeros(date.getSeconds(), 2);
      return `${hh}:${mm}:${ss} ${a}`;
    }

    private leadingZeros(value: number | string, zeros: number): string {
      return ('0000000000' + value).substr(-zeros);
    }

    private get centerScreen(): string {
      if (this.toastLocation === this.center) {
        return 'top: 50vh; margin-top: -6rem;';
      }
      return '';
    }

    private get toasterOptions() {
      return {
        headerColor: this.options.headerColor || 'primary white--text',
        location: this.options.location || Toaster.BottomRight,
        timeout: this.options.timeout !== undefined ? this.options.timeout : 4000,
        showCountdown: this.options.showCountdown !== undefined ? this.options.showCountdown : true,
        showCloseButton: this.options.showCloseButton !== undefined ? this.options.showCloseButton : true,
        infoColor: this.options.infoColor !== undefined ? this.options.infoColor : 'green',
        warnColor: this.options.warnColor !== undefined ? this.options.warnColor : 'yellow darken-3',
        errorColor: this.options.errorColor !== undefined ? this.options.errorColor : 'red',
        hideInfoNotifications: this.options.hideInfoNotifications !== undefined ? this.options.hideInfoNotifications : false,
        hideWarnNotifications: this.options.hideWarnNotifications !== undefined ? this.options.hideWarnNotifications : false,
        hideAllNotifications: this.options.hideAllNotifications !== undefined ? this.options.hideAllNotifications : false,
        notificationOffsetX: this.options.notificationOffsetX !== undefined ? this.options.notificationOffsetX : 0,
        notificationOffsetY: this.options.notificationOffsetY !== undefined ? this.options.notificationOffsetY : 0,
      };
    }

    private get countDown(): string {
      const value = Math.round(this.countDownTime + .5);
      return `${value}s`;
    }

    private get canClose(): boolean {
      return this.toasterOptions.showCloseButton && this.toastTimeout > 0;
    }

    private get canCountdown(): boolean {
      return this.toasterOptions.showCountdown && this.toastTimeout > 0;
    }

    private get showClose(): boolean {
      return this.toastTimeout === Toaster.WaitForClose;
    }

    private get toasterTimeout(): number {
      return this.toastTimeout <= 0 ? indefiniteToast : Number(this.toastTimeout);
    }

    private get filteredNotifications(): any[] {
      return this.showNotifications ? Toaster.notifications : [];
    }

    private get closeIcon(): string {
      return 'font-size: 1rem;';
    }

    private get iconOffset(): string {
      let offset = '';
      if (this.options.notificationOffsetX) offset += `right: ${this.options.notificationOffsetX};`;
      if (this.options.notificationOffsetY) offset += `bottom: ${this.options.notificationOffsetY};`;
      return offset;
    }

    mounted() {
      Toaster.EventBus.$on(Toaster.toastInfo, this.toast);
      Toaster.EventBus.$on(Toaster.toastWarn, this.toast);
      Toaster.EventBus.$on(Toaster.toastError, this.toast);
      Toaster.EventBus.$on(Toaster.toastConfirm, this.confirm);
      Toaster.EventBus.$on(Toaster.toastAlert, this.alert);
      Toaster.EventBus.$on(Toaster.closeToaster, this.closeToast);
      Toaster.EventBus.$on(Toaster.closeConfirmDialog, this.closeConfirmDialog);
      Toaster.EventBus.$on(Toaster.closeAlertDialog, this.closeAlertDialog);
      Toaster.EventBus.$on(Toaster.showNotificationDialog, this.showNotificationDialog);
      Toaster.EventBus.$on(Toaster.clearNotificationList, this.clearNotificationList);
    }
  }
</script>

<style scoped lang="scss">
  .snackbar {
    position: fixed;
    z-index: 999999;
  }
  .zAlert {
    z-index: 999998;
  }
  .countdown-span {
    position: absolute;
    top: 0px;
    right: 1.5rem;
    font-size: .9rem;
    color: #eeeeee;
  }
  .close-span {
    position: absolute;
    top: 0px;
    right: 3px;
  }
  .close-note {
    text-align: right;
    margin-right: 3px;
  }
  .close-button {
    max-height: 1.6rem;
  }
  .noscroll {
    overflow: hidden;
  }
  .zContainer {
    z-index: 999997;
  }
  .notification-window {
    position: fixed;
    right: 0;
    bottom: 0;
    left: calc(100vw - 23rem);
    max-width: 23rem;
    max-height: 95vh;
    height: auto;
    padding-left: .75rem;
    background-color: white;
    border-radius: 0;
    border-top-left-radius: 10px;
    -moz-border-radius-topleft: 10px;
  }
  .top-border {
    border-top: 1px solid gray;
  }
  .vscroll {
    margin-bottom: 5px;
    padding-bottom: 2px;
    overflow-x: hidden;
    overflow-y: scroll;
  }
  .close-notes {
    max-height: 2rem;
    margin-top: .3rem;
    margin-right: 1.5rem;
    margin-bottom: .75rem;
  }
  .note-title {
    font-size: larger;
    color: #3E2723;
    height: 2rem;
  }
  .note-icon {
    position: fixed;
    bottom: 0;
    right: 0;
    font-size: 1rem;
  }
</style>
