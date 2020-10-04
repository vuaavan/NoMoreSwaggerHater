<template>
  <div class="row mb-5 section-block" id="method-block">
    <div class="col-md-12">
      <h3 class="title-block">Method</h3>
    </div>
    <div class="col-md-2">
      <label>Type</label>
      <select class="form-control" v-model="method.type">
        <option value="Get">GET</option>
        <option value="Post">POST</option>
        <option value="Delete">DELETE</option>
        <option value="Put">PUT</option>
        <option value="Patch">PATCH</option>
      </select>
    </div>
     <div class="col-md-2">
          <label>Path</label>
          <input type="text" class="form-control" v-model="method.path">
        </div>
      <div class="col-md-2">
          <label>Description</label>
          <input type="text" class="form-control" v-model="method.description">
        </div>
      <div class="col-md-2">
          <label>Tags</label>
          <input type="text" class="form-control" v-model="method.tags">
        </div>
        <div class="col-md-2">
          <label>OperationId</label>
          <input type="text" class="form-control" v-model="method.operationId">
        </div>  
  </div>
</template>

<script>
export default {
  props: ["method"],
};
</script>

