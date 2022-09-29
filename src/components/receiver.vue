<template>
  <div>
    <v-alert
      text
      dark
      border="left"
      type="info"
      class="mt-5"
    >
      1.若有急件需求者，請務必使用ATM方式繳交費用，完成轉帳後並於上班時間來電詢問，目前提供兩種領取方式，分別為郵寄（郵政便利袋）、親領。
      <br />2.若需到付宅急便者
      (文件送達時須自行負擔130元貨運費用給黑貓宅急便)，請選擇『親領』，並於備註欄位註明『急件，採到付，收件人：０００、收件地址：０００、手機號碼：０００』，並用ATM繳交申請文件費用後，再請於上班時間電洽註冊組確認。
      <br />3.彌封信封乃對方單位需要本校於封口處蓋章者，並非寄回給您的郵件信封喔。若有此需求者，須於備註欄註明彌封方式，未註明者，將不予受理彌封作業。
      <br />4.若有問題，請於上班時間來電詢問：０４－８５１１８８８分機１３９３。
    </v-alert>
    <v-row
      no-gutters
      class="pl-1 pr-1"
    >
      <v-card
        elevation="2"
        style="width: 100%"
      >
        <v-card-title style="font-weight: bold">選擇寄送方式</v-card-title>
        <v-card-text>
          <!--寄送方式-->
          <v-radio-group
            row
            v-model="receiveInfo.method"
          >
            <v-radio
              v-for="item in postOption"
              :key="item.id"
              :label="item.text"
              :value="item.value"
            ></v-radio>
          </v-radio-group>
          <!--掛號郵寄-->
          <div v-show="receiveInfo.method == 0">
            <v-row>
              <v-col cols="6">
                <v-text-field
                  v-model="receiveInfo.name"
                  label="收件人姓名"
                  :rules="[rules.required]"
                ></v-text-field>
              </v-col>
              <v-col cols="6">
                <v-text-field
                  v-model="receiveInfo.phoneNumber"
                  label="手機號碼"
                  :rules="[rules.required, rules.isNum]"
                ></v-text-field>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="receiveInfo.address"
                  label="地址"
                  :rules="[rules.required]"
                ></v-text-field>
              </v-col>
            </v-row>
          </div>
          <div v-show="receiveInfo.method == 2">
            <v-row no-gutters>
              <v-col
                cols="12"
                class="d-flex justify-end align-center"
              >
                <a
                  href="./apply_grade/tools/demo.docx"
                  download
                >
                  <v-btn color="primary">
                    <i
                      class="fas fa-file-word mr-2"
                      style="font-size: 18px; font-weight: 200"
                    ></i>委託書
                  </v-btn>
                </a>
              </v-col>
            </v-row>
            <v-row no-gutters>
              <v-col cols="6">
                <v-text-field
                  v-model="receiveInfo.consignee.name"
                  label="受託者姓名"
                  :rules="[rules.required]"
                ></v-text-field>
              </v-col>
            </v-row>
          </div>
        </v-card-text>
      </v-card>
    </v-row>
    <v-row
      no-gutters
      class="pl-1 pr-1 mt-5"
      v-show="receiveInfo.method != -1"
    >
      <v-card
        elevation="2"
        style="width: 100%"
      >
        <v-card-title style="font-weight: bold">備註</v-card-title>
        <v-card-text>
          <v-row no-gutters>
            <v-col cols="12">
              <v-textarea
                outlined
                name="input-7-4"
                label="備註"
                v-model="receiveInfo.note"
              ></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-row>
    <!--費用確認-->
    <v-row
      no-gutters
      class="pl-1 pr-1 mt-5"
      v-show="receiveInfo.method != -1"
    >
      <v-card
        elevation="2"
        style="width: 100%"
      >
        <v-card-title style="font-weight: bold">費用確認</v-card-title>
        <v-card-text>
          <v-row no-gutters>
            <v-col
              cols="12"
              style="font-size: 1rem; color: black"
              class="d-flex justify-space-between pb-1"
            >
              <div>列印費用</div>
              {{ docCost }}
            </v-col>
            <v-col
              cols="12"
              style="font-size: 1rem; color: black"
              class="d-flex justify-space-between pb-1"
            >
              <div>郵費</div>
              {{ postCost }}
            </v-col>
            <v-col
              cols="12"
              class="pb-1"
            >
              <v-divider></v-divider>
            </v-col>
            <v-col
              cols="12"
              style="font-size: 1.2rem; color: black; font-weight: bold"
              class="d-flex justify-space-between"
            >
              <div>總計</div>
              {{ sumCost }}
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-row>
    <v-row
      no-gutters
      class="d-flex justify-end mt-5"
    >
      <v-btn
        text
        @click="lastPage"
      >上一步</v-btn>
      <v-btn
        color="primary"
        @click="checkRequired"
      >送出</v-btn>
    </v-row>
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
  props: ["totalPrice"],
  updated() {
    const self = this;
    self.updateHeight();
  },
  data() {
    return {
      //post radio group
      postOption: [
        { id: 0, text: "掛號郵寄", value: 0, price: 20 },
        { id: 1, text: "親自領件", value: 1, price: 0 },
        { id: 2, text: "委託領件", value: 2, price: 0 },
      ],
      //msg box
      msgDialog: {
        state: false,
        msg: "",
      },
      //final Data
      receiveInfo: {
        method: -1,
        name: "",
        phoneNumber: "",
        address: "",
        consignee: {
          name: "",
        },
        note: "",
      },
      finalTotalPrice: 0,
      //required rules
      rules: {
        required: (value) => !!value || "必填",
        isNum: (value) => !isNaN(value) || "必須為數字",
      },
    };
  },
  computed: {
    postCost: function () {
      const self = this;
      var cost = null;
      if (self.receiveInfo.method != -1)
        cost = self.postOption.find(
          (element) => element.id == self.receiveInfo.method
        ).price;

      return cost;
    },
    docCost: function () {
      const self = this;
      return self.totalPrice;
      // var cost = 0;
      // self.totalPrice.forEach(element => {
      //   cost += element.data.totalPrice;
      // });
      // return cost;
    },
    sumCost: function () {
      const self = this;
      var cost = 0;
      cost = self.docCost + self.postCost;
      self.finalTotalPrice = cost;
      return cost;
    },
  },
  methods: {
    checkRequired() {
      const self = this;
      var requiredState = true;
      //check required
      if (self.receiveInfo.method == -1) {
        self.msgDialog.msg = "尚未選擇寄方式";
        self.msgDialog.state = true;
      } else if (self.receiveInfo.method == 0) {
        self.receiveInfo.name.length != 0 &&
        self.receiveInfo.phoneNumber.length != 0 &&
        self.receiveInfo.address.length != 0
          ? (requiredState = false)
          : (requiredState = true);
      } else if (self.receiveInfo.method == 2)
        self.receiveInfo.consignee.name.length != 0
          ? (requiredState = false)
          : (requiredState = true);
      else requiredState = false;

      //check required state,if state is false ,then call msg dialog
      if (requiredState) {
        self.msgDialog.msg = "尚有欄位未填寫";
        self.msgDialog.state = true;
      } else self.updateFinal();
    },
    updateFinal() {
      const self = this;
      self.$emit("updateFinal", {
        type: "receiver",
        data: {
          finalTotalPrice: self.finalTotalPrice,
          receiveInfo: self.receiveInfo,
        },
      });
    },
    updateHeight() {
      const self = this;
      self.$emit("updateHeight", {});
    },
    lastPage() {
      const self = this;
      self.$emit("lastPage", 3);
    },
  },
};
</script>