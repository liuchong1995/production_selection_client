<template>
  <div>
    <el-row style="padding-top: 10px">
      <el-col :span="2">
        <h3 style="margin: 0;padding-left: 20px">必选前提</h3>
      </el-col>
      <el-col :span="22" style="margin-top: -10px">
        <el-form label-width="120px">
          <el-form-item label="产品类型">
            <el-select v-model="constraintRequest.productId" clearable placeholder="请选择产品类型" style="width: 300px">
              <el-option v-for="(product,index) in productList" :key="index" :value="product.productId"
                         :label="product.productName"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="类型或部件">
            <el-select v-show="constraintRequest.productId" value-key="categoryId"
                       v-model="currentPremise.firstCategory"
                       placeholder="一级分类" clearable style="width: 200px" size="small" class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in firstCategoryList" :key="category.categoryId">
              </el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentPremise.firstCategory) && !currentPremise.firstCategory.isLeaf"
                       value-key="categoryId" v-model="currentPremise.secondCategory"
                       placeholder="二级分类" clearable style="width: 200px" size="small" class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in secondCategoryList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentPremise.secondCategory) && !currentPremise.secondCategory.isLeaf"
                       value-key="categoryId" v-model="currentPremise.thirdCategory"
                       placeholder="三级分类" clearable style="width: 200px" size="small" class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in thirdCategoryList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentPremise.thirdCategory) && !currentPremise.thirdCategory.isLeaf"
                       value-key="categoryId" v-model="currentPremise.forthCategory"
                       placeholder="四级分类" clearable style="width: 200px"
                       size="small"
                       class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in forthCategoryList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="showPremiseComponent" v-model="currentPremise.component" placeholder="部件" clearable
                       style="width: 200px"
                       value-key="componentId"
                       size="small"
                       class="filter-item">
              <el-option :value="component" :label="component.componentModelNumber"
                         v-for="component in componentList" :key="component.componentId"></el-option>
            </el-select>
            <el-button v-show="!isEmptyObject(currentPremise.firstCategory)" class="filter-item" type="info"
                       size="small"
                       icon="el-icon-check"
                       @click="isExistent ===1 ? addPremiseOrResult(currentPremiseExitList,currentSelectedPremise) : addPremiseOrResult(currentPremiseNonExitList,currentSelectedPremise)">
              选择
            </el-button>
          </el-form-item>
          <el-form-item label="是否存在">
            <el-radio-group v-model="isExistent" size="small">
              <el-radio-button :label="1">存在时</el-radio-button>
              <el-radio-button :label="0">不存在时</el-radio-button>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="存在前提">
            <el-tag v-for="(categoryOrComponent,index) in currentPremiseExitList"
                    :key="index"
                    closable
                    :disable-transitions="false"
                    @close="deletePremiseOrResult(index,currentPremiseExitList)">
              {{categoryOrComponent.categoryName || categoryOrComponent.componentModelNumber}}
            </el-tag>
          </el-form-item>
          <el-form-item label="不存在前提">
            <el-tag v-for="(categoryOrComponent,index) in currentPremiseNonExitList"
                    :key="index"
                    closable
                    :disable-transitions="false"
                    @close="deletePremiseOrResult(index,currentPremiseNonExitList)">
              {{categoryOrComponent.categoryName || categoryOrComponent.componentModelNumber}}
            </el-tag>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="2">
        <h3 style="margin: 0;padding-left: 20px">必选结果</h3>
      </el-col>
      <el-col :span="22" style="margin-top: -10px">
        <el-form label-width="120px">
          <el-form-item label="类型或部件">
            <el-select v-show="constraintRequest.productId" value-key="categoryId" v-model="currentResult.firstCategory"
                       placeholder="一级分类" clearable style="width: 200px"
                       size="small"
                       class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in firstCategoryList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentResult.firstCategory) && !currentResult.firstCategory.isLeaf"
                       value-key="categoryId" v-model="currentResult.secondCategory"
                       placeholder="二级分类" clearable style="width: 200px"
                       size="small"
                       class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in secondCategoryResultList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentResult.secondCategory) && !currentResult.secondCategory.isLeaf"
                       value-key="categoryId" v-model="currentResult.thirdCategory"
                       placeholder="三级分类" clearable style="width: 200px"
                       size="small"
                       class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in thirdCategoryResultList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="!isEmptyObject(currentResult.thirdCategory) && !currentResult.thirdCategory.isLeaf"
                       value-key="categoryId" v-model="currentResult.forthCategory" placeholder="四级分类" clearable
                       style="width: 200px"
                       size="small"
                       class="filter-item">
              <el-option :value="category" :label="category.categoryName"
                         v-for="category in forthCategoryResultList" :key="category.categoryId"></el-option>
            </el-select>
            <el-select v-show="showResultComponent" v-model="currentResult.component" placeholder="部件" clearable
                       style="width: 200px"
                       value-key="componentId"
                       size="small"
                       class="filter-item">
              <el-option :value="component" :label="component.componentModelNumber"
                         v-for="component in componentResultList" :key="component.componentId"></el-option>
            </el-select>
            <el-button v-show="!isEmptyObject(currentResult.firstCategory)" class="filter-item" type="info" size="small"
                       icon="el-icon-check" @click="addPremiseOrResult(currentResultList,currentSelectedResult)">选择
            </el-button>
          </el-form-item>
          <el-form-item label="必选结果">
            <el-tag
              v-for="(categoryOrComponent,index) in currentResultList"
              :key="index"
              closable
              :disable-transitions="false"
              @close="deletePremiseOrResult(index,currentResultList)">
              {{categoryOrComponent.categoryName || categoryOrComponent.componentModelNumber}}
            </el-tag>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
    <el-row>
      <el-col :span="2">
        <h3 style="margin: 0;padding-left: 20px">约束信息</h3>
      </el-col>
      <el-col :span="22" style="margin-top: -10px">
        <el-form label-width="120px">
          <el-form-item label="约束所在组号">
            <el-input v-model="constraintRequest.groupId" placeholder="约束所在组号" style="width: 200px"
                      type="number"></el-input>
          </el-form-item>
          <el-form-item label="约束所在组名">
            <el-input v-model="constraintRequest.groupName" placeholder="约束所在组名" style="width: 300px"></el-input>
          </el-form-item>
          <el-form-item label="约束描述">
            <el-input :rows="3" v-model="constraintRequest.constraintDesc" type="textarea" placeholder="约束描述"
                      style="width: 400px"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="saveConstraint">保存</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>

</template>

<script>
  import { fetchList } from '@/api/product'
  import { getOneLevelCategory } from '@/api/category'
  import { getComponentByLastCate } from '@/api/component'
  import { insertConstraint, getMaxGroupId, addAdvanceMandatory} from '@/api/constraint'

  export default {
    name: 'advanceMandatoryConstraint',
    data() {
      return {
        isExistent: 1,
        productList: [],
        //当前选择的前提和结果用于页面交互
        currentPremise: {},
        currentResult: {},
        //当前选择的前提和结果用于存储
        currentSelectedPremise: {},
        currentSelectedResult: {},
        //当前保存的前提和结果列表
        currentPremiseList: [],
        currentResultList: [],

        currentPremiseExitList: [],
        currentPremiseNonExitList: [],

        //前提列表
        firstCategoryList: [],
        secondCategoryList: [],
        thirdCategoryList: [],
        forthCategoryList: [],
        componentList: [],
        //结果列表
        secondCategoryResultList: [],
        thirdCategoryResultList: [],
        forthCategoryResultList: [],
        componentResultList: [],

        constraintRequest: {
          productId: undefined,
          constraintPremise: {
            categories: [],
            components: []
          },
          constraintOperation: 1,
          constraintResult: {
            categories: [],
            components: []
          },
          groupId: undefined,
          groupName: undefined,
          constraintDesc: undefined
        },
        advanceMandatoryConstraintRequest: {
          productId: undefined,
          exitCate: [],
          exitComp: [],
          nonExistentCate: [],
          nonExistentComp: [],
          resCate: [],
          resComp: [],
          groupId: undefined,
          groupName: undefined,
          constraintDesc: undefined
        }
      }
    },
    mounted() {
      this.initPage()
    },
    computed: {
      showPremiseComponent() {
        return !this.isEmptyObject(this.currentPremise.forthCategory)
          || !this.isEmptyObject(this.currentPremise.firstCategory) && this.currentPremise.firstCategory.isLeaf
          || !this.isEmptyObject(this.currentPremise.secondCategory) && this.currentPremise.secondCategory.isLeaf
          || !this.isEmptyObject(this.currentPremise.thirdCategory) && this.currentPremise.thirdCategory.isLeaf
          || !this.isEmptyObject(this.currentPremise.forthCategory) && this.currentPremise.forthCategory.isLeaf
      },
      showResultComponent() {
        return !this.isEmptyObject(this.currentResult.forthCategory)
          || !this.isEmptyObject(this.currentResult.firstCategory) && this.currentResult.firstCategory.isLeaf
          || !this.isEmptyObject(this.currentResult.secondCategory) && this.currentResult.secondCategory.isLeaf
          || !this.isEmptyObject(this.currentResult.thirdCategory) && this.currentResult.thirdCategory.isLeaf
          || !this.isEmptyObject(this.currentResult.forthCategory) && this.currentResult.forthCategory.isLeaf
      }
    },
    methods: {
      async initPage() {
        [this.productList, this.constraintRequest.groupId] = await Promise.all([fetchList(), getMaxGroupId()])
      },
      uniqueArray(arr) {
        return arr.filter((item, index, array) => array.indexOf(item) === index)
      },
      isEmptyObject(obj) {
        for (let key in obj) {
          return false//返回false，不为空对象
        }
        return true//返回true，为空对象
      },
      addPremiseOrResult(premiseOrResultList, premiseOrResult) {
        if (premiseOrResultList.indexOf(premiseOrResult) === -1) {
          premiseOrResultList.push(premiseOrResult)
        } else {
          this.$message('该类别或部件已存在列表中，请选择其他该类别或部件！')
        }
      },
      deletePremiseOrResult(index, premiseOrResultList) {
        premiseOrResultList.splice(index, 1)
      },
      async saveConstraint() {
        this.advanceMandatoryConstraintRequest.productId = this.constraintRequest.productId
        this.advanceMandatoryConstraintRequest.groupId = this.constraintRequest.groupId
        this.advanceMandatoryConstraintRequest.groupName = this.constraintRequest.groupName
        this.advanceMandatoryConstraintRequest.exitCate = []
        this.advanceMandatoryConstraintRequest.exitComp = []
        this.advanceMandatoryConstraintRequest.nonExistentCate = []
        this.advanceMandatoryConstraintRequest.nonExistentComp = []
        this.advanceMandatoryConstraintRequest.resCate = []
        this.advanceMandatoryConstraintRequest.resComp = []
        for (const premise of this.currentPremiseExitList) {
          if (premise.categoryId) {
            this.advanceMandatoryConstraintRequest.exitCate.push(premise)
          } else {
            this.advanceMandatoryConstraintRequest.exitComp.push(premise)
          }
        }
        for (const premise of this.currentPremiseNonExitList) {
          if (premise.categoryId) {
            this.advanceMandatoryConstraintRequest.nonExistentCate.push(premise)
          } else {
            this.advanceMandatoryConstraintRequest.nonExistentComp.push(premise)
          }
        }
        for (const res of this.currentResultList) {
          if (res.categoryId) {
            this.advanceMandatoryConstraintRequest.resCate.push(res)
          } else {
            this.advanceMandatoryConstraintRequest.resComp.push(res)
          }
        }

        this.advanceMandatoryConstraintRequest.exitCate = this.uniqueArray(this.advanceMandatoryConstraintRequest.exitCate)
        this.advanceMandatoryConstraintRequest.exitComp = this.uniqueArray(this.advanceMandatoryConstraintRequest.exitComp)
        this.advanceMandatoryConstraintRequest.nonExistentCate = this.uniqueArray(this.advanceMandatoryConstraintRequest.nonExistentCate)
        this.advanceMandatoryConstraintRequest.nonExistentComp = this.uniqueArray(this.advanceMandatoryConstraintRequest.nonExistentComp)
        this.advanceMandatoryConstraintRequest.resCate = this.uniqueArray(this.advanceMandatoryConstraintRequest.resCate)
        this.advanceMandatoryConstraintRequest.resComp = this.uniqueArray(this.advanceMandatoryConstraintRequest.resComp)
        const lengthOfPremise = this.advanceMandatoryConstraintRequest.exitCate.length + this.advanceMandatoryConstraintRequest.exitComp.length
        + this.advanceMandatoryConstraintRequest.nonExistentCate.length + this.advanceMandatoryConstraintRequest.nonExistentComp.length
        const lengthOfRes = this.advanceMandatoryConstraintRequest.resCate.length + this.advanceMandatoryConstraintRequest.resComp.length
        if (lengthOfPremise === 0 || lengthOfRes === 0){
          this.$message('输入约束信息不足！')
          return
        }
        this.$confirm('您确定新增这个约束么, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async() => {
          this.advanceMandatoryConstraintRequest.constraintDesc = this.constraintRequest.constraintDesc ? this.constraintRequest.constraintDesc : ''
          await addAdvanceMandatory(this.advanceMandatoryConstraintRequest)
          this.$message('保存约束成功！')
          this.$message({
            type: 'success',
            message: '新增成功!'
          })
        })
      }
    },
    //是一个要命的watch真的优化不动了
    watch: {
      'constraintRequest.productId': async function(newVal) {
        if (newVal) {
          const query = {
            productId: newVal,
            parentId: 0
          }
          this.firstCategoryList = await getOneLevelCategory(query)
        } else {
          this.firstCategoryList = []
        }
        //清空各种分类
        this.secondCategoryList = []
        this.thirdCategoryList = []
        this.forthCategoryList = []
        this.componentList = []
        this.secondCategoryResultList = []
        this.thirdCategoryResultList = []
        this.forthCategoryResultList = []
        this.componentResultList = []
        //清空当前与选择前提and结果
        this.currentPremise = {}
        this.currentResult = {}
        //清空当前与选择结果前提项and结果项
        this.currentSelectedPremise = {}
        this.currentSelectedResult = {}
      },

      'currentPremise.firstCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentPremise.firstCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.secondCategoryList = await getOneLevelCategory(query)
            this.componentList = []
          } else {
            this.secondCategoryList = []
            this.componentList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedPremise = this.currentPremise.firstCategory
        }
        this.thirdCategoryList = []
        this.forthCategoryList = []
        if (this.currentPremise.secondCategory) {
          this.currentPremise.secondCategory = {}
        }
        if (this.currentPremise.thirdCategory) {
          this.currentPremise.thirdCategory = {}
        }
        if (this.currentPremise.forthCategory) {
          this.currentPremise.forthCategory = {}
        }
        if (this.currentPremise.component) {
          this.currentPremise.component = {}
        }
      },

      'currentPremise.secondCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentPremise.secondCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.thirdCategoryList = await getOneLevelCategory(query)
            this.componentList = []
          } else {
            this.thirdCategoryList = []
            this.componentList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedPremise = this.currentPremise.secondCategory
        }
        this.forthCategoryList = []
        if (this.currentPremise.thirdCategory) {
          this.currentPremise.thirdCategory = {}
        }
        if (this.currentPremise.forthCategory) {
          this.currentPremise.forthCategory = {}
        }
        if (this.currentPremise.component) {
          this.currentPremise.component = {}
        }
      },

      'currentPremise.thirdCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentPremise.thirdCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.forthCategoryList = await getOneLevelCategory(query)
            this.componentList = []
          } else {
            this.forthCategoryList = []
            this.componentList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedPremise = this.currentPremise.thirdCategory
        }
        if (this.currentPremise.forthCategory) {
          this.currentPremise.forthCategory = {}
        }
        if (this.currentPremise.component) {
          this.currentPremise.component = {}
        }
      },

      'currentPremise.forthCategory.categoryId': async function(newVal) {
        if (newVal) {
          this.componentList = await getComponentByLastCate(newVal)
          this.currentSelectedPremise = this.currentPremise.forthCategory
        }
        if (this.currentPremise.component) {
          this.currentPremise.component = {}
        }
      },

      'currentPremise.component.componentId': async function(newVal) {
        if (newVal) {
          this.currentSelectedPremise = this.currentPremise.component
        }
      },

      //结果级联
      'currentResult.firstCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentResult.firstCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.secondCategoryResultList = await getOneLevelCategory(query)
            this.componentResultList = []
          } else {
            this.secondCategoryResultList = []
            this.componentResultList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedResult = this.currentResult.firstCategory
        }
        this.thirdCategoryResultList = []
        this.forthCategoryResultList = []
        if (this.currentResult.secondCategory) {
          this.currentResult.secondCategory = {}
        }
        if (this.currentResult.thirdCategory) {
          this.currentResult.thirdCategory = {}
        }
        if (this.currentResult.forthCategory) {
          this.currentResult.forthCategory = {}
        }
        if (this.currentResult.component) {
          this.currentResult.component = {}
        }
      },

      'currentResult.secondCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentResult.secondCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.thirdCategoryResultList = await getOneLevelCategory(query)
            this.componentResultList = []
          } else {
            this.thirdCategoryResultList = []
            this.componentResultList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedResult = this.currentResult.secondCategory
        }
        this.forthCategoryResultList = []
        if (this.currentResult.thirdCategory) {
          this.currentResult.thirdCategory = {}
        }
        if (this.currentResult.forthCategory) {
          this.currentResult.forthCategory = {}
        }
        if (this.currentResult.component) {
          this.currentResult.component = {}
        }
      },

      'currentResult.thirdCategory.categoryId': async function(newVal) {
        if (newVal) {
          if (!this.currentResult.thirdCategory.isLeaf) {
            const query = {
              productId: this.constraintRequest.productId,
              parentId: newVal
            }
            this.forthCategoryResultList = await getOneLevelCategory(query)
            this.componentResultList = []
          } else {
            this.forthCategoryResultList = []
            this.componentResultList = await getComponentByLastCate(newVal)
          }
          this.currentSelectedResult = this.currentResult.thirdCategory
        }
        if (this.currentResult.forthCategory) {
          this.currentResult.forthCategory = {}
        }
        if (this.currentResult.component) {
          this.currentResult.component = {}
        }
      },

      'currentResult.forthCategory.categoryId': async function(newVal) {
        if (newVal) {
          this.componentResultList = await getComponentByLastCate(newVal)
          this.currentSelectedResult = this.currentResult.forthCategory
        }
        if (this.currentResult.component) {
          this.currentResult.component = {}
        }
      },

      'currentResult.component.componentId': async function(newVal) {
        if (newVal) {
          this.currentSelectedResult = this.currentResult.component
        }
      }
    }
  }
</script>
