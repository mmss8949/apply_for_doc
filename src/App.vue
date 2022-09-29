<template>
  <v-app>
    <div
      id="loading"
      :style="{ display: loading ? 'flex' : 'none' }"
    >
      <v-progress-circular
        indeterminate
        color="black"
        :size="70"
      ></v-progress-circular>
    </div>
    <v-container
      fluid
      class="pl-1 pr-2 pt-1"
    >
      <v-stepper v-model="stepState">
        <v-stepper-header>
          <v-stepper-step
            :complete="stepState > 0"
            step="0"
          >申請須知</v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step
            :complete="stepState > 1"
            step="1"
          >聯絡資料</v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step
            :complete="stepState > 2"
            step="2"
          >申請項目</v-stepper-step>
          <v-divider></v-divider>
          <v-stepper-step step="3">領件方式選擇</v-stepper-step>
        </v-stepper-header>

        <v-stepper-items>
          <v-stepper-content step="0">
            <v-alert
              text
              dark
              border="left"
              type="error"
            >因寒暑假期間本校郵政代辦所僅於週二、四協助郵件寄發，故您收件時間將會再延後一到兩個工作天，敬請見諒。</v-alert>
            <v-row
              no-gutters
              class="d-flex justify-end"
            >
              <v-btn text>不同意</v-btn>
              <v-btn
                color="primary"
                @click="
                  stepState = 1;
                  finishStepState[0] = true;
                "
              >我已了解申請須知</v-btn>
            </v-row>
          </v-stepper-content>

          <v-stepper-content step="1">
            <ContactForm
              :infoData="testData.infoData"
              @updateFinal="updateFinal"
              @updateHeight="updateHeight"
              @lastPage="lastPage"
              @nextPage="nextPage"
            ></ContactForm>
          </v-stepper-content>

          <v-stepper-content step="2">
            <InfoForm
              :infoData="testData.infoData"
              @updateFinal="updateFinal"
              @updateHeight="updateHeight"
              @lastPage="lastPage"
              @nextPage="nextPage"
            ></InfoForm>

            <DocApply
              v-show="finalData.info.stId.length != 0"
              :applyItems="testData.applyData"
              :applyYears="testData.yearSem"
              :studentId="finalData.info.stId"
              @updateFinal="updateFinal"
              @updateHeight="updateHeight"
              @lastPage="lastPage"
              @nextPage="nextPage"
            ></DocApply>
          </v-stepper-content>

          <v-stepper-content step="3">
            <Receiver
              :totalPrice="finalData.shoppingPrice"
              @updateFinal="updateFinal"
              @updateHeight="updateHeight"
              @lastPage="lastPage"
              @nextPage="nextPage"
            ></Receiver>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>
    </v-container>
  </v-app>
</template>

<script>
import ContactForm from "./components/contact";
import InfoForm from "./components/info";
import DocApply from "./components/list";
import Receiver from "./components/receiver";

import $ from "jquery";

export default {
  name: "App",

  components: { ContactForm, InfoForm, DocApply, Receiver },
  mounted() {
    const self = this;
    self.updateHeight();
  },
  updated() {
    const self = this;
    self.updateHeight();
  },
  data: () => ({
    axiosCount: 0,
    loading: false,
    stepState: 0,
    finishStepState: [false, false, false, false],
    idSelected: "",

    testData: {
      infoData: [
        {
          id: {
            value: 0,
            text: "大學日間部",
          },
          dept: { value: 0, text: "財務金融學系" },
          info: {
            dept: "財務金融學系",
            stId: "B10716066",
            name: {
              zh: "王高高",
              en: "Peter Wang",
            },
          },
        },
        {
          id: {
            value: 0,
            text: "大學日間部",
          },
          dept: { value: 1, text: "行銷學系" },
          info: {
            dept: "行銷學系",
            stId: "B10716066",
            name: {
              zh: "王高高",
              en: "Peter Wang",
            },
          },
        },
        {
          id: {
            value: 1,
            text: "研究所",
          },
          dept: { value: 3, text: "國際企業所" },
          info: {
            dept: "國際企業所",
            stId: "M10716066",
            name: {
              zh: "王高高",
              en: "Peter Wang",
            },
          },
        },
      ],
      applyData: [
        {
          id: 0,
          text: "中文單學期成績單",
          price: 20,
        },
        {
          id: 1,
          text: "英文單學期成績單",
          price: 30,
        },
        {
          id: 2,
          text: "中文歷年成績單",
          price: 50,
        },
        {
          id: 3,
          text: "英文歷年成績單",
          price: 60,
        },
        {
          id: 4,
          text: "中、英文學位證明書",
          price: 120,
        },
        {
          id: 5,
          text: "休學證明書",
          price: 90,
        },
        {
          id: 6,
          text: "修業證明書",
          price: 100,
        },
      ],
      yearSem: [
        "1041",
        "1042",
        "1051",
        "1052",
        "1061",
        "1062",
        "1071",
        "1072",
        "1081",
        "1082",
      ],
    },
    finalData: {
      contact: {
        phoneNum: "",
        email: "",
      },
      info: {
        edu: "",
        dept: "",
        stId: "",
        name: {
          zh: "",
          en: "",
        },
      },
      shoppingCar: [],
      shoppingPrice: 0,
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
    },
  }),
  methods: {
    updateFinal: function ({ type, data }) {
      const self = this;
      switch (type) {
        case "contact":
          self.finalData.contact.email = data.email;
          self.finalData.contact.phoneNum = data.phoneNum;
          break;
        case "info":
          self.finalData.info.edu = data.idSelected;
          self.finalData.info.dept = data.deptSelected;
          self.finalData.info.stId = data.stId;
          self.finalData.info.name.zh = data.name.zh;
          self.finalData.info.name.en = data.name.en;
          break;
        case "doc":
          self.finalData.shoppingCar = data.shoppingCar;
          self.finalData.shoppingPrice = data.totalPrice;
          break;
        case "receiver":
          self.finalData.receiveInfo.method = data.receiveInfo.method;
          self.finalData.receiveInfo.name = data.receiveInfo.name;
          self.finalData.receiveInfo.phoneNumber = data.receiveInfo.phoneNumber;
          self.finalData.receiveInfo.address = data.receiveInfo.address;
          self.finalData.receiveInfo.consignee.name =
            data.receiveInfo.consignee.name;
          self.finalData.receiveInfo.note = data.receiveInfo.note;
          self.finalData.finalTotalPrice = data.finalTotalPrice;
          self.finishStepState[3] = true;
          self.sendData();
          break;
      }
    },
    sendData() {
      const self = this;
      console.log(self.finalData);
    },
    nextPage: function (index) {
      if (index != 3) {
        const self = this;
        self.stepState = index + 1;
        self.finishStepState[index] = true;
        self.updateHeight();
      }
    },
    lastPage: function (index) {
      if (index != 0) {
        const self = this;
        self.stepState = index - 1;
        self.finishStepState[index] = false;
        self.updateHeight();
      }
    },
    axios: function (url, method, data, callFunction, errorMsg) {
      const self = this;
      self.axiosCount++;
      self.loading = true;
      axios({
        url: url,
        method: method,
        data: data,
        "Content-Type": "application/json",
      })
        .then(function (response) {
          // your action after success
          if (response.data.result == 1) {
            callFunction(response.data.data);
            self.axiosCount--;
            if (self.axiosCount == 0) self.loading = false;
          } else {
            sysDisplayMessage(
              "",
              -1,
              "",
              "",
              null,
              errorMsg + "," + response.data.msg
            );
          }
        })
        .catch(function (error) {
          // your action on error success
          self.loading = false;
          console.log(error);
          sysDisplayMessage("", -1, "", "", null, "連線失敗");
        });
    },
    //更新視窗高度
    updateHeight: function () {
      const self = this;
      self.$nextTick(() => {
        setTimeout(() => {
          if (document.getElementById("app").scrollHeight > 900) {
            var windowHeight = document.body.scrollHeight + 1;
            window.parent.postMessage(
              '{"event": "changeHeight", "value": ' + windowHeight + "}",
              "*"
            );
          }
        }, 1000);
      });
    },
  },
};
</script>
<style lang="scss" scoped>
#loading {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  z-index: 9999;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.6);
}
</style>
