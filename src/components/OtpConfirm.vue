<script>
// This component must send an OTP code (type: Number) to server for verifying some operations

export default {
  name: 'OtpConfirm',
  props: {
    actionType: {
      required: true,
    },
    moneyTransferId: {
      required: false,
    },
    creditId: {
      required: false,
    },
  },
  data() {
    return {
      otp: '',
    };
  },
  methods: {
    async submit() {
      if (this.actionType === 'login') {
        await this.$httpClient.post('/api/login/approve-otp', {
          otp: this.otp,
        });
      } else if (this.actionType === 'signMoneyTransfer') {
        await this.$httpClient.post('/api/v1/documents/money-transfer/send-sms', {
          transferId: this.moneyTransferId,
          otp: this.otp,
        });
      } else if (this.actionType === 'signCredit') {
        await this.$httpClient.post('/api/v1/documents/credit/send-sms', {
          orderId: this.creditId,
          otp: this.otp,
        });
      }
    },
    async resend() {
      if (this.actionType === 'login') {
        await this.$httpClient.post('/api/login/send-sms');
      } else if (this.actionType === 'signMoneyTransfer') {
        await this.$httpClient.post('/api/v1/documents/money-transfer/send-sms', {
          transferId: this.moneyTransferId,
        });
      } else if (this.actionType === 'signCredit') {
        await this.$httpClient.post('/api/v1/documents/credit/send-sms', {
          orderId: this.creditId,
        });
      }
    },
  },
  async beforeMount() {
    await this.resend();
  },
};
</script>

<template>
  <form @submit.prevent="submit">
    <p v-if="actionType === 'signMoneyTransfer'">Sign money transfer operation</p>
    <p v-else-if="actionType === 'signCredit'">Sign document to receive your credit</p>

    <label for="otp">
      Code from sms

      <input
        id="otp"
        name="otp"
        type="text"
        v-model="otp"
        placeholder="Type it here"
      >
    </label>

    <button type="submit">Submit</button>

    <button @click="resend">Resend sms</button>
  </form>
</template>

<style></style>
