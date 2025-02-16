<template>
  <el-card class="box-card">
    <div slot="header">
      <span>経過情報</span>
      <el-button
        type="primary"
        size="small"
        @click="editVisibleChange()"
        >登録</el-button
      >
    </div>
    <el-row :gutter="20">
      <el-col :span="12">
        <table class="detail-table">
          <tbody>
            <tr>
              <th>最終ステータス</th>
              <td>{{ detail.progress.status }}</td>
            </tr>
          </tbody>
        </table>
      </el-col>
      <el-col :span="12">
        <el-button type="info" size="mini" @click="quotationFilesVisible = true"
          >見積書({{ this.$route.params['q_cnt'] }})</el-button
        >
        <el-button type="info" size="mini" @click="photoFilesVisible = true"
          >写真({{ this.$route.params['p_cnt'] }})</el-button
        >
        <el-button type="info" size="mini" @click="reportFilesVisible = true"
          >報告書({{ this.$route.params['r_cnt'] }})</el-button
        >
      </el-col>
    </el-row>
    <table class="detail-table">
      <tr>
        <th rowspan="2">日時</th>
        <th rowspan="2">ステータス</th>
        <th rowspan="2">入力者</th>
        <th rowspan="2">コメント</th>
        <th colspan="2">FAX送信</th>
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
          <td align="center" width="50px">
            {{ item.faxed_to_client == 1 ? '済' : '' }}
          </td>
          <td align="center" width="50px">
            {{ item.faxed_to_shop == 1 ? '済' : '' }}
          </td>
        </tr>
      </template>
    </table>

    <el-dialog
      title="【見積書ファイルリスト】"
      :visible.sync="quotationFilesVisible"
      :width="filedialogWidth"
    >
      <quotation-files :detail="detail" />
      <span slot="footer" class="dialog-footer">
        <el-button @click="quotationFilesVisible = false">閉じる</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="【写真リスト】"
      :visible.sync="photoFilesVisible"
      :width="filedialogWidth"
    >
      <photo-files :detail="detail" />
      <span slot="footer" class="dialog-footer">
        <el-button @click="photoFilesVisible = false">閉じる</el-button>
      </span>
    </el-dialog>

    <el-dialog
      title="【報告書ファイルリスト】"
      :visible.sync="reportFilesVisible"
      :width="filedialogWidth"
    >
      <report-files :detail="detail" />
      <span slot="footer" class="dialog-footer">
        <el-button @click="reportFilesVisible = false">閉じる</el-button>
      </span>
    </el-dialog>

    <el-dialog
      title="経過情報 登録"
      :visible.sync="editVisible"
      :width="editdialogWidth"
      custom-class="slide-dialog"
      top="0px"
    >
      <progress-edit :detail="detail" @create="editVisible = false" />
    </el-dialog>
  </el-card>
</template>

<script>
import QuotationFiles from './sub/QuotationFiles.vue';
import PhotoFiles from './sub/PhotoFiles.vue';
import ReportFiles from './sub/ReportFiles.vue';
import ProgressEdit from './sub/ProgressEdit.vue';

export default {
  components: { QuotationFiles, PhotoFiles, ProgressEdit, ReportFiles },
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
      editVisible: false,
      quotationFilesVisible: false,
      photoFilesVisible: false,
      reportFilesVisible: false,
      progress: [],
      q_cnt: 0,
      r_cnt: 0,
      p_cnt: 0,
      filedialogWidth: '700px',
      editdialogWidth: '60%',
    };
  },
  created() {
    this.progress = {
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
    };
  },
  mounted() {
    const dialogs = document.querySelectorAll('.slide-dialog');
    dialogs.forEach((el) => {
      el.closest('.el-dialog__wrapper').classList.add('slide-dialog-wrapper');
    });

    this.filesCnt();
    this.getBreakDate();

    if(this.isMobile()) {
      this.filedialogWidth = '100%';
      this.editdialogWidth = '100%';
    }

  },
  methods: {
    isMobile() {
      var check = true;
      if(document.querySelector("body").clientWidth > 737) check = false;
      return check;
    },

    filesCnt() {
      var quotation_cnt = 0,
        photo_cnt = 0,
        qphoto_cnt = 0,
        report_cnt = 0;
      this.detail.uploading_files.forEach((el) => {
        if (el.kind == 'quotation') quotation_cnt++;
        if (el.kind == 'photo') photo_cnt++;
        if (el.kind == 'report') report_cnt++;
        if (el.kind == 'quotation_photo') qphoto_cnt ++;
      });

      this.$route.params['q_cnt'] = quotation_cnt;
      this.$route.params['p_cnt'] = photo_cnt;
      this.$route.params['qp_cnt'] = qphoto_cnt;
      this.$route.params['r_cnt'] = report_cnt;
    },

    editVisibleChange() {
      this.editVisible = true;
      document
        .querySelector(
          '#app > div > div.main-container > section > div > div.el-row > div:nth-child(2) > div > div.el-card__body > div:nth-child(6)'
        )
        .classList.remove('close-css');
      var div_modal = document.querySelector("body > div:nth-child(8)");
      if (div_modal) {
        div_modal.classList.add('v-modal');
      }
    },

    formatterProgress(row, column) {
      return this.progress[row.progress_id] ?? '';
    },
    getBreakDate(row, column) {
      for (let i = 0; i < this.detail.maintenance_progress.length; i++) {
        var aa = this.detail.maintenance_progress[i].created_at.split(' ');
        var dd = aa[0] + '<br>' + aa[1];
        this.detail.maintenance_progress[i].created_at = dd;
      }
    },
  },
};
</script>
