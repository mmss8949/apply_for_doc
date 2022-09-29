<template>
  <div>

    <v-row
      no-gutters
      class="pl-1 pr-1"
    >
      <v-card
        elevation="2"
        style="width: 100%"
      >
        <v-card-title style="font-weight: bold">請點選您欲申請文件</v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item-group color="indigo">
              <v-list-item
                v-for="(item, i) in applyItems"
                :key="i"
                @click="
                  applyDetailDialog.state = true;
                  applyDetailDialog.index = item.id;
                "
              >
                <v-list-item-content>
                  <v-row class="align-end">
                    <v-col
                      cols="8"
                      style="font-size: 17px"
                    >{{
                      item.text
                    }}</v-col>
                    <v-col cols="4">
                      <v-row
                        no-gutters
                        style="width: 100px"
                        class="d-flex justify-end align-baseline"
                      >
                        <div style="font-size: 15px">${{ item.price }}</div>
                        <div
                          style="font-size: 13px"
                          class="pl-2"
                        >/份</div>
                      </v-row>
                    </v-col>
                  </v-row>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
        </v-card-text>
      </v-card>
    </v-row>
    <v-row
      no-gutters
      class="mt-5 pl-1 pr-1"
    >
      <v-card
        elevation="2"
        style="width: 100%"
      >
        <v-card-title style="font-weight: bold">已申請文件種類</v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item-group color="indigo">
              <v-list-item
                v-for="(item, i) in shoppingCar"
                :key="i"
              >
                <v-list-item-content>
                  <v-row class="align-end">
                    <v-col
                      cols="6"
                      style="font-size: 17px"
                    >{{
                      item.title
                    }}</v-col>
                    <!-- student id -->
                    <v-col cols="1">
                      {{item.stId}}
                    </v-col>
                    <!-- price -->
                    <v-col cols="3">
                      <v-row
                        no-gutters
                        class="d-flex justify-start align-baseline"
                      >
                        <div
                          style="width: 100px"
                          class="d-flex justify-end align-baseline"
                        >
                          <div style="font-size: 18px">
                            {{ item.data.count }}
                          </div>
                          <div
                            style="font-size: 13px"
                            class="pl-2"
                          >份</div>
                        </div>
                        <div
                          style="width: 80px"
                          class="d-flex justify-end align-baseline"
                        >
                          <div style="font-size: 18px">
                            {{ item.data.totalPrice }}
                          </div>
                          <div
                            style="font-size: 13px"
                            class="pl-2"
                          >元</div>
                        </div>
                      </v-row>
                    </v-col>
                    <!-- action -->
                    <v-col cols="2">
                      <v-row
                        no-gutters
                        class="d-flex justify-center"
                      >
                        <div @click="
                            applyDetailDialog.state = true;
                            applyDetailDialog.index = item.id;
                          ">
                          <i
                            class="far fa-edit"
                            style="font-size: 20px; font-weight: bold"
                          ></i>
                        </div>
                        <div
                          class="ml-10"
                          @click="deleteDoc(item.id)"
                        >
                          <i
                            class="far fa-trash-alt"
                            style="font-size: 20px; font-weight: bold"
                          ></i>
                        </div>
                      </v-row>
                    </v-col>
                  </v-row>
                </v-list-item-content>
              </v-list-item>
              <v-divider></v-divider>
              <v-list-item>
                <v-list-item-content>
                  <v-row class="align-end">
                    <v-col
                      cols="7"
                      style="font-size: 1.2rem; font-weight: bold"
                    >總計</v-col>
                    <v-col cols="3">
                      <v-row
                        no-gutters
                        class="d-flex justify-start align-baseline"
                      >
                        <div
                          style="width: 100px"
                          class="d-flex justify-end align-baseline"
                        ></div>
                        <div
                          style="width: 80px"
                          class="d-flex justify-end align-baseline"
                        >
                          <div style="font-size: 18px">{{ totalPrice() }}</div>
                          <div
                            style="font-size: 13px"
                            class="pl-2"
                          >元</div>
                        </div>
                      </v-row>
                    </v-col>
                  </v-row>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
          </v-list>
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
        @click="checkTextFiled"
      >下一步</v-btn>
    </v-row>
    <!--v-dialog-->
    <!-- <v-dialog v-model="applyDetailDialog.state" persistent max-width="500">
      <v-card v-if="applyDetailDialog.index==0">
        <v-card-title class="headline">中文單學期成績單</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份" v-model="applyDetailDialog.forms[0].count"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="6">
              <v-text-field label="學年度"></v-text-field>
            </v-col>
            <v-col cols="6">
              <v-text-field label="學期"></v-text-field>
            </v-col>
          </v-row>

          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">排名</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>
          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==1">
        <v-card-title class="headline">英文單學期成績單</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="6">
              <v-text-field label="學年度"></v-text-field>
            </v-col>
            <v-col cols="6">
              <v-text-field label="學期"></v-text-field>
            </v-col>
          </v-row>

          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">排名</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>
          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">GPA</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>
          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==2">
        <v-card-title class="headline">中文歷年成績單</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>

          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">排名</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>

          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==3">
        <v-card-title class="headline">英文歷年成績單</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>

          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">排名</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>
          <v-radio-group v-model="radioGroup" row>
            <v-col cols="2" class="pa-0 pr-5">GPA</v-col>
            <v-radio label="是" :value="true"></v-radio>
            <v-radio label="否" :value="false"></v-radio>
          </v-radio-group>
          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==4">
        <v-card-title class="headline">中、英文學位證明書</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==5">
        <v-card-title class="headline">休學證明書</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
      <v-card v-if="applyDetailDialog.index==6">
        <v-card-title class="headline">修業證明書</v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <v-text-field label="份"></v-text-field>
            </v-col>
          </v-row>

          <v-row>
            <v-col cols="12">
              <v-textarea outlined name="input-7-4" label="備註"></v-textarea>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" @click="applyDetailDialog.state = false">儲存</v-btn>
          <v-btn text @click="applyDetailDialog.state = false">取消</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>-->
    <!--v-dialog array-->
    <v-dialog
      v-model="applyDetailDialog.state"
      persistent
      max-width="500"
    >
      <v-card
        v-for="item in applyDetailDialog.forms"
        :key="item.id"
        v-show="applyDetailDialog.index == item.id"
      >
        <v-card-title class="headline">{{ item.title }}</v-card-title>
        <v-card-text>
          <v-row>
            <v-col
              cols="12"
              v-if="item.compose.count"
            >
              <v-text-field
                label="份"
                v-model="item.data.count"
                :rules="[rules.required, rules.isNum]"
              ></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col
              cols="12"
              v-if="item.compose.years"
            >
              <v-select
                dense
                label="學年期"
                :items="applyYears"
                v-model="item.data.years"
              ></v-select>
            </v-col>
          </v-row>
          <!-- <v-row>
            <v-col cols="6" v-if="item.compose.years">
              <v-text-field
                label="學年度"
                v-model="item.data.years"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
            <v-col cols="6" v-if="item.compose.sem">
              <v-text-field
                label="學期"
                v-model="item.data.sem"
                :rules="[rules.required]"
              ></v-text-field>
            </v-col>
          </v-row> -->

          <v-radio-group
            row
            v-if="item.compose.rank"
            v-model="item.data.rank"
          >
            <v-col
              cols="2"
              class="pa-0 pr-4"
            >排名</v-col>
            <v-radio
              label="是"
              :value="true"
            ></v-radio>
            <v-radio
              label="否"
              :value="false"
            ></v-radio>
          </v-radio-group>

          <v-radio-group
            row
            v-if="item.compose.gpa"
            v-model="item.data.gpa"
          >
            <v-col
              cols="2"
              class="pa-0 pr-4"
            >GPA</v-col>
            <v-radio
              label="是"
              :value="true"
            ></v-radio>
            <v-radio
              label="否"
              :value="false"
            ></v-radio>
          </v-radio-group>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            @click="saveDoc(item)"
          >儲存</v-btn>
          <v-btn
            text
            @click="applyDetailDialog.state = false"
          >取消</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <!--msg dialog-->
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
import $ from "jquery";

export default {
  props: ["applyItems", "applyYears", "studentId"],
  updated() {
    const self = this;
    self.updateHeight();
  },
  data() {
    return {
      applyDetailDialog: {
        state: false,
        index: 1,
        forms: [
          {
            id: 0,
            title: "中文單學期成績單",
            compose: {
              count: true,
              years: true,
              sem: true,
              rank: true,
              gpa: false,
            },
            data: {
              count: null,
              years: null,
              sem: null,
              rank: false,
              gpa: -1,
            },
          },
          {
            id: 1,
            title: "英文單學期成績單",
            compose: {
              count: true,
              years: true,
              sem: true,
              rank: true,
              gpa: true,
            },
            data: {
              count: null,
              years: null,
              sem: null,
              rank: false,
              gpa: false,
            },
          },
          {
            id: 2,
            title: "中文歷年成績單",
            compose: {
              count: true,
              years: false,
              sem: false,
              rank: true,
              gpa: false,
            },
            data: {
              count: null,
              years: -1,
              sem: -1,
              rank: false,
              gpa: -1,
            },
          },
          {
            id: 3,
            title: "英文歷年成績單",
            compose: {
              count: true,
              years: false,
              sem: false,
              rank: true,
              gpa: false,
            },
            data: {
              count: null,
              years: -1,
              sem: -1,
              rank: false,
              gpa: -1,
            },
          },
          {
            id: 4,
            title: "中、英文學位證明書",
            compose: {
              count: true,
              years: false,
              sem: false,
              rank: false,
              gpa: false,
            },
            data: {
              count: null,
              years: -1,
              sem: -1,
              rank: -1,
              gpa: -1,
            },
          },
          {
            id: 5,
            title: "休學證明書",
            compose: {
              count: true,
              years: false,
              sem: false,
              rank: false,
              gpa: false,
            },
            data: {
              count: null,
              years: -1,
              sem: -1,
              rank: -1,
              gpa: -1,
            },
          },
          {
            id: 6,
            title: "修業證明書",
            compose: {
              count: true,
              years: false,
              sem: false,
              rank: false,
              gpa: false,
            },
            data: {
              count: null,
              years: -1,
              sem: -1,
              rank: -1,
              gpa: -1,
            },
          },
        ],
      },
      msgDialog: {
        state: false,
        msg: "",
      },
      shoppingCar: [],
      rules: {
        required: (value) => !!value || "必填",
        isNum: (value) => !isNaN(value) || "必須為數字",
      },
    };
  },
  methods: {
    getTotalPrice(id, count) {
      const self = this;
      var element = self.applyItems.find((element) => element.id == id);
      return element.price * count;
    },
    totalPrice() {
      const self = this;
      var price = 0;
      self.shoppingCar.forEach((element) => (price += element.data.totalPrice));
      return price;
    },
    checkRequired(item) {
      var countState = false;
      var yearsState = false;

      if (item.compose.count)
        item.data.count != null ? (countState = false) : (countState = true);
      if (item.compose.years)
        item.data.years != null ? (yearsState = false) : (yearsState = true);

      return countState || yearsState;
    },
    saveDoc(item) {
      const self = this;
      if (!self.checkRequired(item)) {
        var pos = self.shoppingCar.map((e) => e.id).indexOf(item.id);
        item.data.totalPrice = self.getTotalPrice(item.id, item.data.count);
        var tmp = $.extend(true, {}, item);
        tmp.stId = self.studentId;
        if (pos > -1) {
          self.shoppingCar[pos].stId = self.studentId;
        } else {
          self.shoppingCar.push(tmp);
        }
        self.applyDetailDialog.state = false;
      } else {
        self.msgDialog.msg = "尚有欄位未填寫";
        self.msgDialog.state = true;
      }
    },
    deleteDoc(id) {
      const self = this;
      var pos = self.shoppingCar.map((e) => e.id).indexOf(id);
      if (pos > -1) self.shoppingCar.splice(pos);
    },
    checkTextFiled() {
      const self = this;
      if (self.shoppingCar.length == 0) {
        self.msgDialog.msg = "請添加項目";
        self.msgDialog.state = true;
      } else {
        self.updateFinal();
        self.nextPage();
      }
    },
    updateFinal() {
      const self = this;
      self.$emit("updateFinal", {
        type: "doc",
        data: { shoppingCar: self.shoppingCar, totalPrice: self.totalPrice() },
      });
    },
    updateHeight() {
      const self = this;
      self.$emit("updateHeight", {});
    },
    nextPage() {
      const self = this;
      self.$emit("nextPage", 2);
    },
    lastPage() {
      const self = this;
      self.$emit("lastPage", 2);
    },
  },
};
</script>