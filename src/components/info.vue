<template>
  <div>
    <v-row
      no-gutter
      class="d-flex justify-space-between"
    >
      <v-col
        lg="6"
        md="6"
        cols="12"
        class="d-flex align-baseline"
        no-gutter
      >
        <v-select
          dense
          label="身分選擇"
          :items="infoData"
          item-text="id.text"
          item-value="id.value"
          v-model="formModel.idSelected"
          @change="clearForm()"
        ></v-select>
      </v-col>
      <v-col
        lg="6"
        md="6"
        cols="12"
        class="d-flex align-baseline"
        no-gutter
      >
        <v-select
          dense
          label="系所選擇"
          :items="deptData"
          item-text="dept.text"
          item-value="dept.value"
          v-model="formModel.deptSelected"
          @change="setForm()"
        ></v-select>
      </v-col>
    </v-row>
    <div v-show="
        formModel.idSelected.length != 0 && formModel.deptSelected.length != 0
      ">
      <v-row>
        <v-col cols="6">
          <v-text-field
            disabled
            label="系所"
            v-model="formModel.dept"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            disabled
            label="學號"
            v-model="formModel.stId"
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="6">
          <v-text-field
            disabled
            label="姓名"
            v-model="formModel.name.zh"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            disabled
            label="英文姓名"
            v-model="formModel.name.en"
          ></v-text-field>
        </v-col>
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
        idSelected: "",
        deptSelected: "",
        dept: "",
        stId: "",
        name: {
          zh: "",
          en: "",
        },
      },
      rules: {
        required: (value) => !!value || "Required.",
      },
      msgDialog: {
        state: false,
        msg: "",
      },
    };
  },
  methods: {
    setForm() {
      const self = this;
      var ary = [];
      ary = self.infoData.filter(
        (item) => item.id.value == self.formModel.idSelected
      );
      ary = ary.filter(
        (item) => item.dept.value == self.formModel.deptSelected
      );
      self.formModel.dept = ary[0].info.dept;
      self.formModel.stId = ary[0].info.stId;
      self.formModel.name.zh = ary[0].info.name.zh;
      self.formModel.name.en = ary[0].info.name.en;
      self.updateFinal();
    },
    clearForm() {
      const self = this;
      self.formModel.dept = "";
      self.formModel.stId = "";
      self.formModel.name.zh = "";
      self.formModel.name.en = "";
      self.updateFinal();
    },
    updateFinal() {
      const self = this;
      self.$emit("updateFinal", { type: "info", data: self.formModel });
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