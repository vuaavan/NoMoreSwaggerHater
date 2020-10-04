<template>
  <div class="row single-param">
     
        <!-- Portfolio Item 1-->
        <div class="col-md-2">
          <label>Property</label>
          <input type="text" class="form-control" v-model="request.property">
        </div>
        <!-- <div class="col-md-2">
          <label>Description</label>
          <input type="text" class="form-control" v-model="param.description">
        </div> -->
        <!-- <div class="col-md-2">
          <label>Location (in)</label>
          <select class="form-control" v-model="param.in">
            <option value="query">Query</option>
            <option value="path">Path</option>
            <option value="header">Header</option>
            <option value="cookie">Cookie</option>
          </select>
        </div>
        <div class="col-md-1 text-center">
          <label>Required</label>
          <input type="checkbox" class="form-control"  v-model="param.required" name="check" style="width : 20px; margin: 0 auto">
        </div> -->
        <div class="col-md-2 text-center">
          <label>Type</label>
          <select class="form-control"  v-model="request.type">
            <option value="integer">Integer</option>
            <option value="string">String</option>
          </select>
        </div>
      </div>
</template>

<script>
export default {
  props : ['request']
}
</script>

