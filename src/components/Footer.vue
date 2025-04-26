<template>
  <footer class="app-footer" role="contentinfo">
    <div class="footer-content">
      <p>
        <a :href="`tel:${headerData?.phone}`" aria-label="Call the developer">{{ headerData.phone }}</a> | 
        <a :href="headerData?.['linked-in']" target="_blank" aria-label="Visit LinkedIn profile">LinkedIn</a> | 
        <a :href="`mailto:${headerData?.email}`" aria-label="Send an email">{{ headerData.email }}</a>
      </p>
      <p>&copy; 2025 {{ headerData.name }}. All rights reserved.</p>
    </div>
  </footer>
</template>

<script>
export default {
  props: {
    headerData: Object,
  },
};
</script>