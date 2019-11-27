<template>
  <div class="question-container">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-button>新增试题</el-button>
        <el-button>批量导入</el-button>
      </div>
      <!-- 数据筛选 -->
      <el-form :inline="true" :model="questionParams" class="demo-form-inline">
        <el-form-item label="学科">
          <el-select v-model="questionParams.subjectID" placeholder="请选择">
            <el-option
              v-for="subject in subjects"
              :key="subject.value"
              :label="subject.label"
              :value="subject.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度">
          <el-select v-model="questionParams.difficulty" placeholder="请选择">
            <el-option
              v-for="difficulty in difficulties"
              :key="difficulty.value"
              :label="difficulty.label"
              :value="difficulty.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="题型">
          <el-select v-model="questionParams.questionType" placeholder="请选择">
            <el-option
              v-for="type in questionTypes"
              :key="type.value"
              :label="type.label"
              :value="type.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-select v-model="questionParams.tags" placeholder="请选择">
            <el-option
              v-for="tag in tags"
              :key="tag.value"
              :label="tag.label"
              :value="tag.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市">
          <!-- 省 -->
          <el-select v-model="questionParams.province" placeholder="请选择">
            <el-option
              v-for="province in provinces"
              :key="province"
              :label="province"
              :value="province">
            </el-option>
          </el-select>

          <!-- 市 -->
          <el-select v-model="questionParams.city" placeholder="请选择">
            <el-option
              v-for="city in cities"
              :key="city"
              :label="city"
              :value="city">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
      </el-form>
      <!-- /数据筛选 -->

      <!-- 数据列表 -->
      <el-table
        :data="questions"
        style="width: 100%">
        <!-- 将 type 设置为 index，该列就是一个自动序号列 -->
        <el-table-column
          type="index"
          label="序号">
        </el-table-column>
        <el-table-column
          prop="number"
          label="试题编号">
        </el-table-column>
        <el-table-column
          prop="subject"
          label="学科">
        </el-table-column>
        <el-table-column
          prop="questionType"
          label="题型">
        </el-table-column>
        <el-table-column
          prop="question"
          label="题干">
        </el-table-column>
        <el-table-column
          prop="addDate"
          label="录入时间">
        </el-table-column>
        <el-table-column
          prop="difficulty"
          label="难度">
        </el-table-column>
        <el-table-column
          prop="date"
          label="使用次数">
        </el-table-column>
        <el-table-column
          prop="creator"
          label="录入人">
        </el-table-column>
        <el-table-column
          label="操作">
          <template>
            <div>
              <el-button size="mini" type="info">预览</el-button>
              <br>
              <el-button size="mini" type="primary">修改</el-button>
              <br>
              <el-button size="mini" type="danger">删除</el-button>
              <br>
              <el-button size="mini" type="success">加入精选</el-button>
            </div>
          </template>
        </el-table-column>
      </el-table>
      <!-- /数据列表 -->
    </el-card>
  </div>
</template>

<script>
import request from '@/utils/request'

// 按需加载
import {
  difficulty as difficulties,
  questionType as questionTypes
} from '@/api/hmmm/constants'
// difficulty = 模块.difficulty
// direction = 模块.direction

// 加载所有
// import * as constants from '@/api/hmmm/constants'
// constants.status
// constants.questionType

// import {
//   provinces as getCities,
//   citys as getAreas
// } from '@/api/hmmm/citys'

import addressData from '@/module-form/components/address-data'

export default {
  name: 'QuestionsList',
  data () {
    return {
      // 存储提交给后端的问题列表的查询参数
      questionParams: {
        subjectID: '', // 学科id
        difficulty: '', // 难度
        questionType: '', // 题型
        tags: '', // 标签
        province: '', // 省份
        city: '' // 城市
      },
      questions: [], // 问题列表
      subjects: [], // 学科列表
      difficulties, // 难度列表
      questionTypes, // 题型列表
      tags: [], // 标签列表
      provinces: Object.keys(addressData) // 城市列表
    }
  },

  computed: {
    cities () {
      let data = []
      const province = this.questionParams.province
      if (province) {
        data = Object.keys(addressData[province])
      }
      return data
    }
  },

  created () {
    // 加载问题列表
    this.loadQuestions()

    // 加载学科列表
    this.loadSubjects()

    // 加载标签列表
    this.loadTags()
  },

  methods: {
    onSubmit () {
      // 重新查询获取数据列表
      this.loadQuestions()
    },

    /**
     * 加载基础题库列表
     */
    loadQuestions () {
      request({
        method: 'GET',
        url: '/questions',
        params: this.questionParams
      }).then(res => {
        this.questions = res.data.items
      })
    },

    loadSubjects () {
      request({
        method: 'GET',
        url: '/subjects/simple'
      }).then(res => {
        this.subjects = res.data
      })
    },

    loadTags () {
      request({
        method: 'GET',
        url: '/tags/simple'
      }).then(res => {
        this.tags = res.data
      })
    }
  }
}
</script>

<style lang="scss">
.el-button {
  margin-bottom: 5px;
}
</style>
