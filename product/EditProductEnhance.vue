<template>
  <div v-if="data !== null">
    <div class="title">
      <b @click="redirect('/products')">
        <label class="text-primary action-link">Products</label>
      </b>
      <label class="text-primary">/ {{data.title}}</label>
    </div>
    <div class="product-item-holder">
      <div class="product-item-details">
        <div v-if="errorMessage !== null">
          <label class="text-danger">Opps! {{errorMessage}}</label>
        </div>
        <div class="product-item-title">
          <label>Title <label class="text-danger">*</label></label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.title" placeholder="Type product title here..." :disabled="data.status === 'published'">
        </div>
        <div class="product-item-title">
          <label>Description <label class="text-danger">*</label></label>
          <br>
          <textarea class="form-control" rows="20" v-model="data.description" placeholder="Type product description here..."></textarea>
        </div>
        <div class="product-item-title">
          <label>Tags</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.tags" placeholder="Separate tags with ,">
        </div>
        <div class="product-item-title">
          <label>SKU</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.sku" placeholder="Type product sku here...">
        </div>
        <div v-if="common.ecommerce.productUnits !== null">
          <div v-if="data.variation !== null">
            <div class="product-item-title" style="width: 79%; margin-right: 1%;">
              <label>Volume</label>
              <br>
              <input type="number" class="form-control form-control-custom" v-model="data.variation[0].payload_value" placeholder="Type volume here...">
            </div>
            <div class="product-item-title" style="width: 20%;">
              <label>Units</label>
              <br>
              <select class="form-control form-control-custom" v-model="data.variation[0].payload">
                <option v-for="(item, index) in common.ecommerce.productUnits" :value="item">{{item}}</option>
              </select>
            </div>
          </div>
          <div v-else>
            <div class="product-item-title" style="width: 79%; margin-right: 1%;">
              <label>Volume</label>
              <br>
              <input type="number" class="form-control form-control-custom" v-model="newAttribute.payload_value" placeholder="Type volume here...">
            </div>
            <div class="product-item-title" style="width: 20%;">
              <label>Units</label>
              <br>
              <select class="form-control form-control-custom" v-model="newAttribute.payload">
                <option v-for="(item, index) in common.ecommerce.productUnits" :value="item">{{item}}</option>
              </select>
            </div>
          </div>
        </div>
        <div class="product-item-title">
          <label>Status</label>
          <br>
          <select class="form-control form-control-custom" v-model="data.status">
            <option value="pending">Pending</option>
            <option value="published">Published</option>
          </select>
        </div>
        <div class="product-item-title" style="width: 90%">
          <label>Activity Group</label>
          <br>
          <select class="form-control form-control-custom"  v-model="group">
            <option v-for="(group, index) in groups" :key="index" :value="group">{{group}}</option>
          </select>
        </div>
        <div class="product-item-title pl-4" style="width: 10%; margin-top: 5.5%;">
              <button class="btn btn-primary" @click="addGroup"><i class="fa fa-plus"></i></button>
        </div>
        <div class="table-responsive">
          <table class="table table-hover table-bordered table-sm w-50" v-if="listGroup.length > 0">
              <thead >
                  <tr>
                    <td>Group</td>
                    <td>Action</td>
                  </tr>
              </thead>
              <tbody>
                <tr v-for="(group, index) in listGroup" :key="index">
                  <td>{{group.group}}</td>
                  <td><button class="btn" @click="removeGroup(index)" style="width:20%; background-color: transparent"><i class="fa fa-trash" style="color: red"></i></button></td>
                </tr>
              </tbody>
          </table>
          </div>
        <div class="product-item-title" style="width: 48%; margin-right: 1%;">
              <label>Actives</label>
              <br>
              <input type="text" class="form-control form-control-custom" v-model="active.active_name" placeholder="Active constituents">
            </div>
            <div class="product-item-title" style="width: 20%; margin-right: 1%; margin-top: 2.5%;">
              <label></label>
              <br>
              <input type="number" class="form-control form-control-custom" v-model="active.value" placeholder="value">
            </div>
            <div class="product-item-title" style="width: 20%; margin-top: 2.5%;">
              <label></label>
              <br>
              <select class="form-control form-control-custom" v-model="active.attribute">
                <option v-for="(item, index) in common.ecommerce.productUnits" :value="item">{{item}}</option>
              </select>
            </div>
            <div class="product-item-title pl-4" style="width: 10%; margin-top: 5.5%;">
              <button class="btn btn-primary" @click="addActive"><i class="fa fa-plus"></i></button>
            </div>
            <div class="table-responsive">
            <table class="table table-hover table-bordered table-sm w-50 " v-if="actives === null || actives.length > 0">
              <thead>
                  <tr>
                    <td>Active Constituent</td>
                    <td>Value</td>
                    <td>Attribute</td>
                    <td>Action</td>
                  </tr>
              </thead>
                <tbody v-if="actives === null || actives.length > 0">
                  <tr v-for="(active, index) in actives" :key="index">
                    <td>{{active.active_name}}</td>
                    <td>{{active.value}}</td>
                    <td>{{active.attribute}}</td>
                    <td><button class="btn" @click="removeActive(index)" style="width:20%; background-color: transparent"><i class="fa fa-trash" style="color: red"></i></button></td>
                  </tr>
                </tbody>
            </table>
            </div>
        <div class="product-item-title">
          <label>Solvent (if applicable)</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.details.solvent" placeholder="Solvent">
        </div>
         <div class="product-item-title">
          <label>Other scheduled ingredients</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.details.other_ingredient" placeholder="Other ingredients">
        </div>
         <div class="product-item-title">
          <label>Mixing Order</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.details.mixing_order" placeholder="Type product sku here...">
        </div>
        <div class="product-item-title">
          <label>Formulation</label>
          <br>
          <select class="form-control form-control-custom" v-model="data.details.formulation">
            <option v-for="(formulation, index) in formulations.FORMULATION" :key="index" :value="formulation">{{formulation}}</option>
          </select>
        </div>
        <div class="product-item-title">
          <label>Application Safety Equipment</label>
          <br>
          <div class="form-check">
            
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment1" value="Cotton overalls buttoned to neck and wrist"><span>Cotton overalls buttoned to neck and wrist</span>
            </label>
          </div>
          <div class="form-check">
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment2" value="A Washable hat "><span>A Washable hat</span> 
            </label>
          </div>
          <div class="form-check">
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment3" value="Elbow-lenght PVC gloves"><span>Elbow-lengTH PVC gloves</span>  
            </label>
          </div>
           <div class="form-check">
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment3" value="Face shield or googles"><span>Face shield or googles</span>  
            </label>
          </div>
           <div class="form-check">
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment3" value="Half facepiece respirator or disposable respirator"><span>Half facepiece respirator or disposable respirator</span>  
            </label>
          </div>
           <div class="form-check">
            <label class="form-check-label">
              <input type="checkbox" class="form-check-input" v-model="data.details.safety_equipment" id="equipment3" value="Full respirator"><span>Full respirator</span>  
            </label>
          </div>
        </div>
        <div class="product-item-title" style="width: 79%; margin-right: 1%;">
          <label>Shelf Life</label>
          <br>
          <input type="number" class="form-control form-control-custom" v-model="data.details.shelf_life" placeholder="Type shelf life">
        </div>
        <div class="product-item-title" style="width: 20%; margin-top:2.3%">
          <br>
          <input type="text" class="form-control form-control-custom" placeholder="Months" disabled>
        </div>
         <div class="product-item-title">
          <label>APVMA Approval number</label>
          <br>
          <input type="text" class="form-control form-control-custom" v-model="data.details.approval_number" placeholder="Approval number">
        </div>
         <div class="product-item-title">
          <label>APVMA Approval date</label>
          <br>
          <input type="date" class="form-control form-control-custom" v-model="data.details.approval_date" placeholder="Approval date">
        </div>
        <div class="product-item-title">
          <button class="btn btn-danger" @click="showConfirmationModal(data.id)" v-if="data.inventories === null && data.product_traces === null && data.status === 'pending'" style="margin-top: 5px;">Delete</button>
          <button class="btn btn-primary pull-right" @click="updateProduct()" style="margin-right: 2px; margin-top: 5px;">Update</button>
          <button class="btn btn-warning pull-right" @click="redirect('/marketplace/product/' + data.code + '/' + 'preview')" style="margin-right: 10px; margin-top: 5px;">Preview</button>
        </div>
      </div>
      <images :data="data"/>
    </div>
    <div class="product-more-details">
      <div class="pagination-holder">
        <ul class="product-menu">
          <li v-for="item, index in productMenu" v-bind:class="{'menu-active': item.flag === true}" class="" @click="selectMenu(index)">{{item.title}}</li>
        </ul>
      </div>
      <div class="details-holder" v-if="selectedMenu.title === 'Variation'">
        <variations :item="data"></variations>
      </div>
      <div class="details-holder" v-if="selectedMenu.title === 'Price'">
        <prices :item="data"></prices>
      </div>
      <div class="details-holder" v-if="selectedMenu.title === 'Inventory'">
        <inventories :item="data" v-if="common.ecommerce.inventoryType === 'inventory'"></inventories>
        <product-trace v-if="common.ecommerce.inventoryType === 'product_trace'" :item="data"></product-trace>
      </div>
      <div class="details-holder" v-if="selectedMenu.title === 'Comment'">
        <product-comments :payloadValue="data.id" :payload="'product'" :load="true"></product-comments>
      </div>

      <div class="details-holder" v-if="selectedMenu.title === 'Bundled Products'">
        <bundled-products :item="data"></bundled-products>
      </div>

      <div class="details-holder" v-if="selectedMenu.title === 'Other Details'">
        <other-details @file1="getFiles($event, 'file1')" @file2="getFiles($event, 'file2')" v-bind:item="data"></other-details>
      </div>
    </div>
    <browse-images-modal></browse-images-modal>
    <confirmation ref="confirmationModal" :title="'Confirmation Message'" :message="'Are you sure you want delete this product?'" @onConfirm="deleteProduct($event.id)"></confirmation>
  </div>
</template>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import COMMON from 'src/common.js'
import GROUP from './Group.js'
import axios from 'axios'
export default {
  mounted(){
    this.retrieve()
  },
  data(){
    return {
      user: AUTH.user,
      config: CONFIG,
      sample: [],
      errorMessage: null,
      data: null,
      formulations: GROUP,
      code: this.$route.params.code,
      prevMenuIndex: 0,
      selectedMenu: COMMON.ecommerce.editProductMenu[0],
      selectedImage: null,
      qty: 1,
      priceFlag: false,
      newImage: {
        product_id: null,
        url: null,
        status: null
      },
      imageStatus: null,
      common: COMMON,
      newAttribute: {
        product_id: null,
        payload: null,
        payload_value: null
      },
      details: {
        group: null,
        solvent: null,
        safety: null,
        formulation: null,
        active: [],
        safety_equipment: [],
        mixing_order: []
      },
      groups: [],
      actives: [],
      active: {
        active_name: null,
        value: null,
        attribute: null
      },
      group: null,
      listGroup: []
    }
  },
  computed: {
    productMenu: function (){
      if(this.data !== null){
        return (this.data.type === 'regular') ? [{
          title: 'Inventory',
          flag: true
        }, {title: 'Other Details',
          flag: false}] : COMMON.ecommerce.editProductMenu
      }
    }
  },
  components: {
    'ratings': require('components/increment/generic/rating/Ratings.vue'),
    'product-comments': require('components/increment/generic/comment/Comments.vue'),
    'browse-images-modal': require('components/increment/generic/image/BrowseModal.vue'),
    'variations': require('components/increment/ecommerce/product/Variations.vue'),
    'inventories': require('components/increment/ecommerce/product/Inventories.vue'),
    'product-trace': require('components/increment/ecommerce/product/ProductTrace.vue'),
    'bundled-products': require('components/increment/ecommerce/product/BundledProducts.vue'),
    'prices': require('components/increment/ecommerce/product/Prices.vue'),
    'confirmation': require('components/increment/generic/modal/Confirmation.vue'),
    'images': require('components/increment/ecommerce/product/Images.vue'),
    'other-details': require('components/increment/ecommerce/product/OtherDetailsEnhance.vue')
  },
  methods: {
    redirect(parameter){
      ROUTER.push(parameter)
    },
    selectMenu(index){
      if(this.prevMenuIndex !== index){
        this.productMenu[this.prevMenuIndex].flag = false
        this.productMenu[index].flag = true
        this.prevMenuIndex = index
        this.selectedMenu = this.productMenu[index]
      }
    },
    addActive(){
      if(this.active.active_name === null || this.active.value === null || this.active.attribute === null){
        this.errorMessage = 'Empty fields cannot be added'
        return
      }
      let active = {
        active_name: this.active.active_name,
        value: this.active.value,
        attribute: this.active.attribute
      }
      if(this.actives.length < 3){
        this.actives.push(active)
        this.active.active_name = null
        this.active.value = null
        this.active.attribute = null
      }else{
        this.errorMessage = 'Active already reach max number(3)'
      }
    },
    removeActive(index){
      this.actives.splice(index, 1)
    },
    addGroup(){
      if(this.group === null){
        this.errorMessage = 'Empty field cannot be added'
        return
      }
      let group = {
        group: this.group
      }
      this.listGroup.push(group)
      this.group = null
    },
    removeGroup(index){
      this.listGroup.splice(index, 1)
    },
    showConfirmationModal(id){
      this.$refs.confirmationModal.show(id)
    },
    selectImage(url){
      this.selectedImage = url
    },
    samples(data){
      console.log(data)
    },
    getFiles(data, fileNumber){
      if(data !== null){
        if(fileNumber === 'file1'){
          console.log('data1', data)
          console.log('file1', this.data.details)
          this.data.details.files.label.title = data.title
          this.data.details.files.label.url = data.url
          this.updateProduct()
          this.retrieve()
        }else{
          console.log('file2s', this.data.details)
          this.data.details.files.sds.title = data.title
          this.data.details.files.sds.url = data.url
          this.updateProduct()
          this.retrieve()
        }
      }
    },
    retrieve(){
      let parameter = {
        condition: [{
          value: this.code,
          column: 'code',
          clause: '='
        }],
        account_id: this.user.userID,
        inventory_type: this.common.ecommerce.inventoryType
      }
      $('#loading').css({display: 'block'})
      this.APIRequest('products/retrieve', parameter).then(response => {
        $('#loading').css({display: 'none'})
        console.log(response.data)
        if(response.data.length > 0){
          this.data = response.data[0]
          this.actives = this.data.details.active
          this.listGroup = this.data.details.group != null ? this.data.details.group : []
          console.log(this.actives)
          this.tagChecker(this.data)
        }
      })
    },
    deleteProduct(id){
      let parameter = {
        id: id
      }
      $('#loading').css({display: 'block'})
      this.APIRequest('products/delete', parameter).then(response => {
        $('#loading').css({display: 'none'})
        ROUTER.push('/products')
      })
    },
    tagChecker(data){
      if(data.tags.includes('insecticide')){
        this.groups = GROUP.INSECTICIDE
      }else if(data.tags.includes('herbicide')){
        this.groups = GROUP.HERBICIDE
      }else if(data.tags.includes('fungicide')){
        this.groups = GROUP.FUNGICIDE
      }else{
        this.groups = GROUP.ADJUVANT
      }
    },
    validate(){
      this.errorMessage = null
      if(this.data.title === null || this.data.title === ''){
        this.errorMessage = 'Title is required.'
        return false
      }
      if(this.data.description === '' || this.data.description === null){
        this.errorMessage = 'Description is required.'
        return false
      }
      if(typeof this.common.ecommerce.productTitleLimit !== undefined && typeof this.common.ecommerce.productTitleLimit !== 'undefined' && this.data.title.length > this.common.ecommerce.productTitleLimit){
        this.errorMessage = 'Product title length should not exceed to ' + this.common.ecommerce.productTitleLimit + ' characters.'
        return false
      }
      if(parseInt(this.data.details.active.value) <= 0 || parseInt(this.data.details.shelf_life) <= 0){
        this.errorMessage = 'Fields should be greater than 0'
        return false
      }
      if(this.data.variation !== null){
        if(this.data.variation[0].payload === '' || this.data.variation[0].payload === null){
          this.errorMessage = 'Unit is required'
          return false
        }
      }else{
        if(this.newAttribute.payload === '' || this.newAttribute.payload === null){
          this.errorMessage = 'Unit is required'
          return false
        }
      }
      return true
    },
    updateProduct(){
      if(this.validate() === false){
        return
      }
      this.data.details.group = this.listGroup
      this.data.details.active = this.actives
      this.data.details = JSON.stringify(this.data.details)
      console.log(this.data.details)
      $('#loading').css({display: 'block'})
      this.APIRequest('products/update', this.data).then(response => {
        $('#loading').css({display: 'none'})
        if(this.common.ecommerce.productUnits !== null){
          if(this.data.variation !== null){
            this.updateAttribute(this.data.variation[0])
          }else{
            this.createAttribute()
          }
        }else{
          this.retrieve()
        }
      })
    },
    createAttribute(){
      if(this.newAttribute.payload_value !== null && this.newAttribute.payload_value !== '' && parseInt(this.newAttribute.payload_value) > 0){
        this.newAttribute.product_id = this.data.id
        this.APIRequest('product_attributes/create', this.newAttribute).then(response => {
          if(response.data > 0){
            this.newAttribute.payload_value = null
            this.errorMessage = null
            this.retrieve()
          }
        })
      }else{
        this.retrieve()
      }
    },
    updateAttribute(item){
      if(item.payload_value !== null && item.payload_value !== '' && parseInt(item.payload_value) > 0){
        this.APIRequest('product_attributes/update', item).then(response => {
          if(response.data === true){
            this.retrieve()
          }else{
            this.retrieve()
          }
        })
      }else{
        this.retrieve()
      }
    },
    showImages(status){
      this.imageStatus = status
      $('#browseImagesModal').modal('show')
    },
    createPhoto(url){
      if(this.imageStatus === 'featured'){
        this.newImage.status = 'featured'
        if(this.data.featured === null){
          this.newImage.product_id = this.data.id
          this.newImage.url = url
          console.log('new')
          this.createRequest(this.newImage)
        }else{
          console.log('old')
          this.data.featured[0].url = url
          this.updateRequest(this.data.featured[0])
        }
      }else if(this.imageStatus === 'images'){
        this.newImage.status = 'others'
        this.newImage.product_id = this.data.id
        this.newImage.url = url
        console.log(this.newImage)
        this.createRequest(this.newImage)
      }
    },
    createRequest(parameter){
      $('#loading').css({display: 'block'})
      this.APIRequest('product_images/create', parameter).then(response => {
        $('#loading').css({display: 'none'})
        this.retrieve()
      })
    },
    updateRequest(parameter){
      $('#loading').css({display: 'block'})
      this.APIRequest('product_images/update', parameter).then(response => {
        $('#loading').css({display: 'none'})
        this.retrieve()
      })
    },
    manageImageUrl(url, status){
      console.log('fsdafs')
      this.imageStatus = status
      this.createPhoto(url)
    },
    removeImage(id){
      let parameter = {
        id: id
      }
      this.APIRequest('product_images/delete', parameter).then(response => {
        this.retrieve()
        this.selectedImage = null
      })
    }
  }
}
</script>
<style scoped>
  .title{
    width: 95%;
    float: left;
    margin-left: 2%;
    font-size: 16px;
  }
  .product-item-holder{
    width: 98%;
    float: left;
    margin-left: 2%;
    min-height: 10px;
    overflow-y: hidden;
  }
  .product-image{
    width: 36%;
    float: left;
    margin-left: 2%;
    margin-right: 2%;
    min-height: 410px;
    overflow-y: hidden;
    text-align: center;
  }
  .product-image .main-image{
    height: 350px;
    max-width: 100%;
  }
  .product-image .fa-image{
    font-size: 250px;
    line-height: 350px;
  }
  .product-image .image-item{
    height: 60px;
    float: left;
    width: 80px;
    text-align: center;
  }
  .product-image .other-image{
    height: 60px;
    max-width: 100%;
  }
  .product-image .image-item:hover{
    cursor: pointer;
    background: #ffaa81;
  }
  .images-holder .overlay{
    height: 60px;
    z-index: 2;
    width: 80px;
    margin-top: -60px;
    float: left;
    background: rgba(0, 0, 0, 0);
  }
  .images-holder{
    width: 100%;
    float: left;
    min-height: 60px;
    overflow-y: hidden;
  }
  .product-item-details{
    min-height: 50px;
    width: 60%;
    float: left;
    overflow-y: hidden;
    border: 0px;
  }
  .product-item-title{
    width: 100%;
    float: left;
    min-height: 50px;
    overflow-y: hidden;
    margin-top: 15px;
  }
  .product-item-title label{
    font-weight: 600;
  }
  .product-row{
    width: 100%;
    float: left;
    min-height: 40px;
    overflow-y: hidden;
    font-weight: 600;
    font-size: 16px;
    line-height: 40px;
  }
  .product-row-tags{
    width: 100%;
    float: left;
    min-height: 40px;
    overflow-y: hidden;
    font-weight: 600;
    line-height: 40px;
  }
  .product-row-rating{
    width: 100%;
    float: left;
    min-height: 40px;
    overflow-y: hidden;
    font-size: 16px;
    line-height: 40px;
  }
  .product-row label{
    float: left;
    width: 15%;
  }
  .qty-input{
    width: 50px;
    height: 40px;
    float: left;
    border-radius: 5px;
    border: solid 1px #ffaa81;
    text-align: center !important;
  }
  .product-row-tags label{
    float: left;
  }
  .tag-label{
    height: 35px;
    line-height: 35px;
    background: #ffaa81;
    padding-left: 10px;
    padding-right: 10px;
    color: #fff;
    margin-right: 5px;
    border-radius: 5px;
    margin-top: 2px;
  }
  .attribute{
    width: 50px;
    height: 40px;
    float: left;
    border-radius: 5px;
    border: solid 1px #ffaa81;
    margin-right: 5px;
  }
  .attribute:hover{
    cursor: pointer;
  }
  .product-more-details{
    width: 96%;
    float: left;
    margin-bottom: 100px;
    min-height: 50px;
    overflow-y: hidden;
    margin-left: 2%;
    margin-right: 2%;
    border-top: solid 1px #ffaa81;
    margin-top: 25px;
  }
  .product-more-details .details-holder{
    width: 60%;
    float: left;
    min-height: 10px;
    overflow-y: hidden;
    margin-top: 25px;
  }

  .product-more-details .details-holder-bundled{
    width: 100%;
    float: left;
    min-height: 10px;
    overflow-y: hidden;
    margin-top: 25px;
  }

  .product-menu{
    list-style: none;
    padding: 0px;
    margin: 0;
    width: 60%;
    margin-right: 40%;
    float: left;
    color: #fff;
  }
  .product-menu li{
    height: 50px;
    float: left;
    width: 25%;
    line-height: 50px;
    padding-left: 10px;
    font-weight: 600;
    border-right: solid 1px #fff;
    background: #ffaa81;
  }
  .product-menu li:hover{
    cursor: pointer;
    color: #000;
  }
  .menu-active{
    color: #000;
  }
  .show-prices:hover{
    cursor: pointer;
    color: #ffaa81;
  }
  .form-control-custom{
    height: 50px !important;
  }

  .remove-image{
    position: absolute;
  }

  #featured-image-remove{
    top: 50px;
    right: 5px;
    z-index: 1000;
    font-size: 24px;
  }

  #other-images-remove{
    top: -20px;
    right: 0px;
    z-index: 1000;
    font-size: 18px;
  }

  .remove-image:hover{
    cursor: pointer;
  }

  @media (max-width: 992px){
    .product-item-details, .product-image, .product-more-details .details-holder, .product-menu{
      width: 100%;
    }
  }
</style>
