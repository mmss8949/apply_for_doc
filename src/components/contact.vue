<template>
  <div>

    <div>
      <v-row>
        <v-col cols="6">
          <v-text-field
            v-model="formModel.phoneNum"
            label="輸入手機號碼"
            :rules="[rules.required,rules.isNum, rules.phoneNumCounter]"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            v-model="formModel.email"
            :rules="[rules.required, rules.email]"
            label="輸入E-mail"
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row
        v-if="formModel.email.length != 0 && formModel.phoneNum.length != 0"
        no-gutters
        class="d-flex justify-end"
      >
        <v-btn
          text
          @click="lastPage"
        >上一步</v-btn>
        <v-btn
          color="primary"
          @click="checkTextFiled"
        >下一步</v-btn>
      </v-row>
    </div>
    <v-dialog
      v-model="msgDialog.state"
      max-width="500"
    >
      <v-card>
        <v-card-title class="headline">訊息</v-card-title>
        <v-card-text>{{ msgDialog.msg }}</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            text
            @click="msgDialog.state = false"
          >確認</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
<script>
export default {
  props: ["infoData"],
  updated() {
    const self = this;
    self.updateHeight();
  },
  data() {
    return {
      formModel: {
        email: "",
        phoneNum: "",
      },
      rules: {
        required: (value) => !!value || "Required.",
        phoneNumCounter: (value) =>
          value.length == 10 || "Must be 10 characters",
        email: (value) => {
          const pattern =
            /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
          return pattern.test(value) || "Invalid e-mail.";
        },
        isNum: (value) => !isNaN(value) || "必須為數字",
      },
      msgDialog: {
        state: false,
        msg: "",
      },
    };
  },
  methods: {
    checkEmailFormat(value) {
      const pattern =
        /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return pattern.test(value) || false;
    },
    checkTextFiled() {
      const self = this;
      if (
        self.formModel.email.length == 0 ||
        self.formModel.phoneNum.length == 0
      ) {
        self.msgDialog.msg = "尚有欄位未填";
        self.msgDialog.state = true;
      } else if (!self.checkEmailFormat(self.formModel.email)) {
        self.msgDialog.msg = "Email格式錯誤";
        self.msgDialog.state = true;
      } else {
        self.updateFinal();
        self.nextPage();
      }
    },
    updateFinal() {
      const self = this;
      self.$emit("updateFinal", { type: "contact", data: self.formModel });
    },
    updateHeight() {
      const self = this;
      self.$emit("updateHeight", {});
    },
    nextPage() {
      const self = this;
      self.$emit("nextPage", 1);
    },
    lastPage() {
      const self = this;
      self.$emit("lastPage", 1);
    },
  },
  computed: {
    deptData: function () {
      const self = this;
      var ary = [];
      if (self.formModel.idSelected.length != 0)
        ary = self.infoData.filter(
          (item) => item.id.value == self.formModel.idSelected
        );
      return ary;
    },
  },
};
</script>