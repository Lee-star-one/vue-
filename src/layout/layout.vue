<template>
  <div class="layout" :class="layoutClass">
    <slot></slot>
  </div>
</template>
<script>
export default {
  name:"GuluLayout",
  data(){
    return {
        layoutClass:{
          hasSider:false
      }
    }
  },
  created(){
    console.log(this.$children)
    
  },
  mounted(){
    this.$children.forEach((vm)=>{
      if(vm.$options.name==="GuluSider"){
        this.layoutClass.hasSider = true
      }
    })
  }
}
</script>
<style lang="less" scoped>
.layout{
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  &.hasSider{
    flex-direction: row;
  }
}
</style>