<!-- filepath: /Users/leviathan/Documents/Github_Projects/resume_portfolio/resume-portfolio/src/components/NavigationMenu.vue -->
<template>
  <nav class="navigation-menu">
    <button
      class="menu-toggle"
      @click="$emit('toggleMenu')"
      aria-label="Toggle navigation menu"
      :aria-expanded="isMenuOpen"
    >
      ☰
    </button>
    <ul :class="{ 'menu-open': isMenuOpen, 'desktop-menu': !isMenuOpen }">
      <li v-for="heading in headings" :key="heading.id">
        <a :href="`#${heading.id}`" @click="$emit('closeMenu')" :aria-label="`Go to ${heading.text} section`">
          {{ heading.text }}
        </a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    headings: Array,
    isMenuOpen: Boolean,
  },
};
</script>