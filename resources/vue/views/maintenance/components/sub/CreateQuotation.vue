<template>
  <div>
    <el-row :gutter="20">
      <el-col :span="10">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>日時</th>
              <td class="input-td">
                <datetime
                  v-model="date"
                  type="datetime"
                  input-class="my-class"
                  placeholder="日付を選択してください。"
                  :format="{
                    year: 'numeric',
                    month: '2-digit',
                    day: '2-digit',
                    hour: 'numeric',
                    minute: '2-digit',
                    hour12: false,
                  }"
                  :phrases="{ ok: 'Continue', cancel: 'Exit' }"
                  :hour-step="1"
                  :minute-step="60"
                  :week-start="7"
                  auto
                ></datetime>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
      <el-col :span="9" :offset="5">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>入力者</th>
              <td>{{ userName }}</td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="10">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>種類</th>
              <td class="input-td">
                <el-select v-model="kind" placeholder="種類"  filterable
                  clearable  class="filter-item">
                    <el-option label="事前見積" :value="1" />
                    <el-option label="修正見積" :value="2" />
                    <el-option label="提案見積" :value="3" />
                    <el-option label="事後見積" :value="4" />
                </el-select>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="10">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>金額</th>
              <td class="input-td">
                <currency-input
                  v-model="amount"
                  :options="{ currency: 'JPY' }"
                  class="el-input__inner"
                />
                <!-- <input v-model="amount" /> -->
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="15">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>摘要</th>
              <td class="input-td">
                <input v-model="comment" class="el-input__inner" />
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="15">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>見積書</th>
              <td style="border: none; padding: 0 5px">
                <el-upload
                  ref="uploadQuotation"
                  :action="
                    '/api/v2/maintenance/upload/quotation/' +
                    detail.maintenance_id
                  "
                  :auto-upload="false"
                  :limit="1"
                  :multiple="false"
                >
                  <el-button slot="trigger" size="small" type="info"
                    >ファイル選択</el-button
                  >
                </el-upload>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row :gutter="20">
      <el-col :span="15">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>写真</th>
              <td style="border: none; padding: 0 5px">
                <el-upload
                  ref="uploadQuotationPhoto"
                  :action="
                    '/api/v2/maintenance/uploadQuotation/photo/' + detail.maintenance_id
                  "
                  :auto-upload="false"
                  :limit="1"
                  :multiple="false"
                >
                  <el-button slot="trigger" size="small" type="info"
                    >ファイル選択</el-button
                  >
                </el-upload>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <div style="text-align: right">
      <el-button type="primary" size="small" @click="save()">登録</el-button>
      <el-button type="default" size="small" @click="handleClose()">閉じる</el-button>
    </div>
    <el-table
      :data="detail.quotation_info"
      :show-header="true"
      border
      style="width: 100%; margin-top: 2%"
    >
      <el-table-column align="center" prop="date" label="日時" />
      <el-table-column align="center" prop="kind" :formatter="formatterKind" label="種類" />
      <el-table-column
        align="center"
        prop="amount"
        label="金額"
        :formatter="formatterCurrency"
      />
      <el-table-column align="center" prop="comment" label="摘要" />
      <el-table-column
        align="center"
        prop="quotation_files_cnt"
        label="見積書"
      />
      <el-table-column align="center" prop="photo_files_cnt" label="写真" />
      <el-table-column
        align="center"
        prop="editor"
        label="入力者"
        width="100"
      />
      <el-table-column align="center" label="削除">
        <template slot-scope="scope">
            <el-button size="small" type="primary" @click="deleteQuotationId(scope.row.quotation_info_id)" style="    background-color: transparent;
    border: 0px;"><i class="material-icons" style="font-size:48px;color:red">&#xe92b;</i></el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>
<style>
.close-css {
  display: none;
}
</style>
<style lang="scss" scoped>
</style>
    <script src="https://unpkg.com/vue"></script>
    <!-- Optional polyfills for usage with IE 11 -->
    <script src="https://polyfill.io/v3/polyfill.min.js?features=Array.prototype.includes%2CObject.assign"></script>
    <script src="https://unpkg.com/vue-currency-input@1.22.4"></script>
<script>
import MaintenanceResource from '@/api/maintenance';
import CurrencyInput from './CurrencyInput.vue';
import 'vue-datetime/dist/vue-datetime.css';
import { Datetime } from 'vue-datetime';
import { Settings } from 'luxon';
import { DateTime } from 'luxon';

Settings.defaultLocale = 'ja';
const resource = new MaintenanceResource();

export default {
  components: { CurrencyInput, Datetime },
  props: {
    detail: {
      type: Object,
      default: () => {
        return {};
      },
    },
  },
  data() {
    return {
      kind: '',
      quotationKind: [],
      userName: '',
      comment: '',
      date: '',
      amount: '',
      q_cnt: 0,
      p_cnt: 0,
    };
  },
  created() {
    this.$store.dispatch('user/getInfo').then((user) => {
      this.userName = user.name;
    });
    this.quotationKind = {
      1: '事前見積',
      2: '修正見積',
      3: '提案見積',
      4: '事後見積',
    };    
  },
  mounted() {
    this.filesCnt();
  },
  methods: {
    deleteQuotationId(id){

      if(confirm('削除していいですか？')) {
        var data = {
          maintenance_id: this.detail.maintenance_id,
        }
        resource.deleteQuotationId(id, data).then((res) => {
          this.detail.quotation_info = res;
          this.$emit('create');
        });
      } else{
        return;
      }
    },
    handleClose(){
      document.querySelector("#app > div > div.main-container > section > div > div.el-dialog__wrapper.slide-dialog-wrapper > div > div.el-dialog__body > div > div:nth-child(1) > div.el-dialog__wrapper.slide-dialog-wrapper").click();
    },

    filesCnt() {
      var quotation_cnt = 0,
        qphoto_cnt = 0;
      this.detail.quotation_info.forEach((el) => {
        if (el.photo_files_cnt > 0) qphoto_cnt++;
        if (el.quotation_files_cnt > 0) quotation_cnt++;
      });

      this.$route.params['q_cnt'] = quotation_cnt;
      this.$route.params['qp_cnt'] = qphoto_cnt;
    },

    formatterCurrency(row, column) {
      if (row.amount == null) return;
      return '¥' + row.amount;
    },

    formatterKind(row, column){
      if(row.kind == '') return;
      return this.quotationKind[row.kind];
    },

    save() {
      var quotatioPhotoCnt = this.$refs.uploadQuotationPhoto.uploadFiles.length;
      var quotationCnt = this.$refs.uploadQuotation.uploadFiles.length;

      this.$refs.uploadQuotationPhoto.submit();
      this.$refs.uploadQuotation.submit();
      const insertData = {
        date: DateTime.fromISO(this.date).toFormat('yyyy-MM-dd hh:mm'),
        kind: this.kind,
        comment: this.comment,
        amount: this.amount,
        quotation_files_cnt: quotationCnt,
        photo_files_cnt: quotatioPhotoCnt,
        editor: this.userName,
      };
      resource
        .createQuotation(this.detail.maintenance_id, insertData)
        .then((res) => {
          this.$refs.uploadQuotationPhoto.clearFiles();
          this.$refs.uploadQuotation.clearFiles();
          this.detail.quotation_info = res;
          this.comment = '';
          this.kind = '';
          this.amount = '';
          this.faxedToClient = false;
          this.faxedToShop = false;

          this.filesCnt();
          // this.$emit('create');
        });
    },


  },
};
</script>
