<template>
  <div>
    <transition name="modal">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container">
            <div class="modal-default-button" @click="$emit('close')">
              X
            </div>
            <div class="modal-body">
              <slot name="body">
                default body
              </slot>
            </div>
          </div>
        </div>
      </div>
    </transition>

  </div>
</template>

<script>
export default {
  name: 'Modal',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
