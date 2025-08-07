<template>
  <div id="app">
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
</style>
