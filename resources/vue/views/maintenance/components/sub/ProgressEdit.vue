<template>
  <div>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
    <el-row style="left: 41.66%">
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
              <th>現状ステータス*</th>
              <td>
                {{ detail.progress.status }}
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
      <el-col :span="2">
        <p style="text-align: center">
          <i class="fa fa-long-arrow-right" aria-hidden="true"></i>
        </p>
      </el-col>
      <el-col :span="8">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>変更後ステータス*</th>
              <td class="select-td">
                <el-select v-model="progressId" size="small" :multiple="false" placeholder="一一一" clearable style="width: 100%" class="filter-item">
                  <el-option label="一一一" :value="0" />
                  <el-option label="BM承認待" :value="1" />
                  <el-option label="BM承認" :value="2" />
                  <el-option label="BM差戻し" :value="3" />
                  <el-option label="BM却下" :value="4" />
                  <el-option label="BM保留" :value="5" />
                  <el-option label="受付前" :value="6" />
                  <!-- <el-option label="本部承認" :value="1" /> -->
                  <el-option label="本部差戻し" :value="7" />
                  <el-option label="本部見送り" :value="8" />
                  <el-option label="依頼前" :value="9" />
                  <el-option label="依頼済" :value="10" />
                  <el-option label="見積待ち" :value="11" />
                  <el-option label="入荷待ち" :value="13" />
                  <el-option label="DM承認待ち" :value="14" />
                  <el-option label="稟議中" :value="15" />
                  <el-option label="見積発注済み" :value="16" />
                  <el-option label="訪問待ち" :value="18" />
                  <el-option label="報告待ち" :value="19" />
                  <el-option label="店完了" :value="20" />
                  <el-option label="取完了" :value="21" />
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
              <th>対応期限*</th>
              <td class="input-td">
                <datetime v-model="time1" valueType="format" placeholder="日付を選択してください。" ></datetime>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="10">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>写真</th>
              <td style="border:none;padding:0 5px;">
                <el-upload ref="uploadPhoto" :action="'/api/v2/maintenance/upload/photo/' + detail.maintenance_id" :auto-upload="false" :multiple="false" :on-success="getUploadFiles()">
                  <el-button slot="trigger" size="small" type="info">ファイル選択</el-button>
                </el-upload>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="10">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>報告書</th>
              <td style="border:none;padding:0 5px;">
                <el-upload ref="uploadReport" :action="'/api/v2/maintenance/upload/report/' + detail.maintenance_id" :auto-upload="false" :multiple="false" :on-success="getUploadFiles()">
                  <el-button slot="trigger" size="small" type="info">ファイル選択</el-button>
                </el-upload>
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="24">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>経過情報</th>
              <td style="padding:0 5px;">
                <textarea v-model="comment" style="border:none;height:70px;width:100%" />
              </td>
            </tr>
          </tbody>
        </table>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="1">
        <el-checkbox v-model="faxedToShop" style="padding:10px" />
      </el-col>
      <el-col :span="10">
        <table class="detail-table">
          <tr>
            <th style="width:40px;">店</th>
            <th>訪問予定連絡</th>
            <td style="border:none;padding:0 5px;">
              <el-button type="info" size="small">プレビュー</el-button>
            </td>
          </tr>
        </table>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="1">
        <el-checkbox v-model="faxedToClient" style="padding:10px" />
      </el-col>
      <el-col :span="10">
        <table class="detail-table">
          <tr>
            <th style="width:40px;">取</th>
            <th>進捗報告依頼</th>
            <td style="border:none;padding:0 5px;">
              <el-button type="info" size="small">プレビュー</el-button>
            </td>
          </tr>
        </table>
      </el-col>
    </el-row>
    <div style="text-align:right; padding-bottom: 15px;">
      <el-button type="primary" size="small" @click="save()">登録</el-button>
      <el-button type="default" size="small"  @click="handleClose()"  ref="Dialog" >閉じる</el-button>
    </div>
      <table  class="detail-table">
        <tr>
          <th rowspan="2">日時</th>
          <th rowspan="2">ステータス</th>
          <th rowspan="2">入力者</th>
          <th rowspan="2">コメント</th>
          <th colspan="2">
            FAX送信
          </th>       
        </tr>
        <tr>
          <th style="width: 50px">取</th>
          <th style="width: 50px">店</th>        
        </tr>
        <template v-for="item in detail.maintenance_progress">
          <tr>
            <td align="center">
              <span v-html="item.created_at"></span>
            </td>
            <td align="center">{{ progress[item.progress_id] }}</td>
            <td align="center">{{ item.entered_by.name }}</td>
            <td align="center">{{ item.comment }}</td>
            <td align="center" width="50px">{{ item.faxed_to_client == 1 ? '済' : '' }}</td>
            <td align="center" width="50px">{{ item.faxed_to_shop == 1 ? '済' : '' }}</td>
          </tr>
        </template>
      </table>
  </div>
</template>

<style>
  .close-css {
    display: none;
  }

  input.mx-input {
    font-size: 16px;
  }

  .mx-datepicker {
    width: -webkit-fill-available;
  }

  @media screen and (max-width: 737px) {
    #app > div > div.main-container > section > div > div.el-row > div:nth-child(2) > div > div.el-card__body > div.el-dialog__wrapper.slide-dialog-wrapper > div > div.el-dialog__body > div > div:nth-child(5) > div.el-col.el-col-10 {
      width: 75%;
      padding-left: 15px;
    }
    #app > div > div.main-container > section > div > div.el-row > div:nth-child(2) > div > div.el-card__body > div.el-dialog__wrapper.slide-dialog-wrapper > div > div.el-dialog__body > div > div:nth-child(6) > div.el-col.el-col-10 {
      width: 75%;
      padding: 5px 10px 10px 10px;     
    }
  }

</style>

<script>
import MaintenanceResource from '@/api/maintenance';
import 'vue-datetime/dist/vue-datetime.css';
import { Datetime } from 'vue-datetime';
const resource = new MaintenanceResource();

export default {
  components: { Datetime },
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
      time1: null,
      cond1: true,
      userName: '',
      comment: '',
      faxedToClient: 0,
      faxedToShop: 0,
      progressId: 0,
      progress: {
        1: 'BM承認待ち',
        2: 'BM承認',
        3: 'BM差戻し',
        4: 'BM却下',
        5: 'BM保留',
        6: '本部受付前',
        7: '本部差戻し',
        8: '本部見送り',
        9: '依頼確定',
        10: '依頼済',
        11: '見積待ち',
        12: '見積精査中',
        13: '入荷待ち',
        14: 'DM承認待ち',
        15: '稟議中',
        16: '見積発注済み',
        17: '日程調整中',
        18: '訪問待ち',
        19: '報告待ち',
        20: '店完了',
        21: '取完了',
        22: '問合せ中',
      },
    };
  },
  created(){
    this.$store.dispatch('user/getInfo').then(user => {
      this.userName = user.name;
    });
  },

  methods: {
    handleClose(){
      document.querySelector("#app > div > div.main-container > section > div > div.el-row > div:nth-child(2) > div > div.el-card__body > div.el-dialog__wrapper.slide-dialog-wrapper").click();
    },

    save() {
      // if(this.currentStatus == this.detail.progress.status) {
      //   this.$emit('create');
      //   return;
      // } 

      this.$refs.uploadReport.submit();
      this.$refs.uploadPhoto.submit();
      const insertData = {
        progress_id: this.progressId,
        comment: this.comment,
        faxed_to_client: this.faxedToClient,                                                                                                    
        faxed_to_shop: this.faxedToShop,
        deadline_date: this.time1,
      };

      resource.createProgress(this.detail.maintenance_id, insertData).then(res => {
        this.detail.maintenance_progress = res;
        this.detail.progress_id = this.progressId;
        this.detail.progress = {
          progress_id: this.progressId,
          status: this.progress[this.progressId],
        };

        this.getUploadFiles();

        this.filesCnt();
       
        this.comment = '';
        this.faxedToClient = false;
        this.faxedToShop = false;
        this.$emit('create');
      });
    },

    filesCnt() {
      var quotation_cnt = 0,
        photo_cnt = 0,
        report_cnt = 0;
      this.detail.uploading_files.forEach((el) => {
        if (el.kind == 'quotation') quotation_cnt++;
        if (el.kind == 'photo') photo_cnt++;
        if (el.kind == 'report') report_cnt++;
      });

      this.$route.params['q_cnt'] = quotation_cnt;
      this.$route.params['p_cnt'] = photo_cnt;
      this.$route.params['r_cnt'] = report_cnt;

    },

    getUploadFiles() {
      resource.getUploadFiles(this.detail.maintenance_id).then((files) => {
        this.detail.uploading_files = files;
      });
    },

  },
};
</script>
