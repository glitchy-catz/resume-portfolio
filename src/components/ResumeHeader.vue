<template>
  <section class="resume-header non-portrait-only">
    <div class="header-container">
      <a :href="headerData['linked-in']" target="_blank" aria-label="Visit LinkedIn profile" class="contact-link">
        <img
          class="profile-image"
          :src="headerData['profile-image']"
          :alt="headerData.profileAlt"
        />
      </a>
      <div class="header-content">
        <h1>{{ headerData.name }}</h1>
        <p>{{ headerData.title }}</p>
      </div>
    </div>
    <div class="contact-links">
      <p>
        <a class="contact-link" :href="`tel:${headerData.phone}`" aria-label="Call the developer">{{ headerData.phone }}</a> | 
        <a class="contact-link" :href="headerData['linked-in']" target="_blank" aria-label="Visit LinkedIn profile">LinkedIn</a> | 
        <a class="contact-link" :href="`mailto:${headerData.email}`" aria-label="Send an email">{{ headerData.email }}</a>
      </p>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    headerData: Object,
  },
};
</script>