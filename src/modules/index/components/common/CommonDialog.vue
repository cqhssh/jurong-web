<template>
  <div class="common-dialog">
    <el-button @click="add" v-show="showBtn">新增</el-button>
    <el-dialog
      :title="title"
      width="40%"
      :visible.sync="dialogVisible"
      :before-close="handleClose">
      <el-form  :model="form" ref="form"  :label-position="'left'">
        <el-form-item v-for="item in formColumns" :key="item.label" :label="item.label" label-width="120px">
            <el-col :span="20">
              <el-input v-model="form[item.key]" v-if="item.type === 'text' || item.type==='textarea'" :type="item.type" :disabled="disabled"></el-input>
            </el-col>
            <el-col :span="20">
              <el-select v-model="form[item.key]" v-if="item.type === 'select'" style="width: 100%" :disabled="disabled">
                <el-option v-for="opItem in item.options" :value="opItem.value" :label="opItem.label" :key="opItem.value"></el-option>
              </el-select>
            </el-col>
            <!--预留富文本编辑-->
              <editor width="85%" v-if="item.type === 'editor'" element-id="addEditor" v-model="form[item.key]" :value="form[item.key]" ></editor>
            <div v-if="item.type === 'datePicker'">
              <el-col :span="20">
                <el-date-picker
                  :disabled="disabled"
                  type="date"
                  v-model = "form[item.key]"
                  placeholder="选择日期"
                  :value-format="'yyyy-MM-ddTHH:mm:ss'"
                  style="width: 100%"
                >
                </el-date-picker>
              </el-col>
            </div>
          <div v-if="item.type === 'carousel'" class="carousel">
            <el-carousel height="150px">
              <el-carousel-item v-for="(list,$index) in item.options" :key="$index">
                <h3>{{ list.value }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
          <el-upload
            v-if = "item.type === 'img'"
            action=""
            multiple
            :auto-upload="false"
            list-type="picture-card"
            :on-preview="handlePictureCardPreview"
            :on-remove="handleRemove">
            <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="imgDialogVisible">
            <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>
        </el-form-item>
      </el-form>
      <div class="footer">
        <span slot="footer" class="dialog-footer">
                <el-button @click="cancel">取 消</el-button>
                <el-button type="primary" @click="submit">确 定</el-button>
        </span>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import vue from 'vue';
  import Editor from '../../../../common/components/Editor';
    export default {
        name: 'CommonDialog',
        props: {
          formColumns: Array,
          showBtn: Boolean,
          disabled: Boolean
        },
      data () {
        return {
          title: '新增',
          form: {},
          imageUrl: '',
          dialogVisible: false,
          // disabled: false,
          showEditor: false,
          dateTime: '',
          imgDialogVisible: false,
          dialogImageUrl: ''
        }
      },
      components: {
        Editor
      },
      created () {
        this.showEditor = false;
      },
      mounted () {
          this.initFormParams();
      },
      methods: {
          /**
          *  初始化form表单的参数， 为了防止undefined赋不上值
          * */
          initFormParams () {
            for (let i in this.formColumns) {
              if (this.formColumns[i].showDefault) {
                this.form[this.formColumns[i].key] = this.formColumns[i].options[0].label;
              } else {
                this.form[this.formColumns[i].key] = '';
              }
            }
          },
          add () {
            this.title = '新增';
            this.dialogVisible = true;
            this.form = {};
            this.showEditor = true;
            vue.set(this, 'form', {});
          },
        handleClose (done) {
          // this.$emit('submit');
          this.$confirm('确认关闭？')
            .then(_ => {
              // this.from = {};
              this.dialogVisible = false;
              this.form = {};
              this.showEditor = false;
              this.$emit('submit');
              done();
            })
            .catch(_ => {

            });
        },
        handleAvatarSuccess (res, file) {
          this.imageUrl = URL.createObjectURL(file.raw);
        },
        submit () {
            /**
             * 新增/修改接口
             * */
          this.dialogVisible = false;
          this.$emit('submit', this.form);
          this.form = {};
        },
        // 清空数据
        cancel () {
          this.dialogVisible = false;
          this.form ={};
        },
        handlePictureCardPreview (file) {
          this.dialogImageUrl = file.url;
          // this.imgDialogVisible = true;
        },
        handleRemove (file) {
          console.log(file);
        }
      }
    }
</script>

<style scoped lang="less">
.common-dialog{
  .carousel{
    width:380px;
    height:150px;
  }
  .footer{
    text-align: left;
    margin-left:120px;
  }
}
</style>
