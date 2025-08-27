<template>
  <div id="app">
    <vue-progress-bar></vue-progress-bar>
    <router-view />
  </div>

</template>

<script>

export default {
  name: 'App',
  components: {
   
  },
  mounted() {
    // Patch focus trap issue for vue-loading-overlay
    document.querySelectorAll('.vld-overlay').forEach(el => {
      el.addEventListener('focusin', (e) => {
        if (document.querySelector('.modal.show')) return;
        e.preventDefault();
        e.stopPropagation();
        // el.focus(); // optional
      }, { capture: true });
    });
  }
}
</script>
<style>
.swal2-popup .swal2-modal .swal2-icon-warning .swal2-show {
  display: block
}
.vue-progress-bar {
        position: fixed !important;
        top: 0 !important;
        left: 0;
        width: 100%;
        height: 4px !important;
        z-index: 99999 !important;
        opacity: 1 !important;
        background-color: #29d !important;
      }
</style>
