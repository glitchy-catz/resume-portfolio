<template>
  <div id="app">
    <a href="#main-content" class="skip-link">Skip to Content</a>
    <NavigationMenu :headings="headings" :isMenuOpen="isMenuOpen" @toggleMenu="toggleMenu" @closeMenu="closeMenu" />
    <main id="main-content" class="app-main" role="main">
      <Loader v-if="isLoading" />
      <div v-else class="resume">
        <ResumeHeader :headerData="headerData" />
        <div class="resume-content" v-html="parsedMarkdown"></div>
      </div>
    </main>
    <Footer :headerData="headerData" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import { marked } from 'marked';
import NavigationMenu from './components/NavigationMenu.vue';
import ResumeHeader from './components/ResumeHeader.vue';
import Footer from './components/Footer.vue';
import Loader from './components/Loader.vue';

export default {
  components: {
    NavigationMenu,
    ResumeHeader,
    Footer,
    Loader
  },
  setup() {
    const markdownContent = ref('');
    const parsedMarkdown = ref('');
    const headerData = ref({
      name: '',
      title: '',
      ['profile-image']: '',
      profileAlt: '',
      phone: '',
      email: '',
      ['linked-in']: '',
    });
    const isLoading = ref(true);
    const error = ref(null);
    const headings = ref([]);

    function extractData(markdown, regex) {
      const match = markdown.match(regex);
      return match ? match[1] : '';
    }

    function extractHeadings(html) {
      const tempDiv = document.createElement('div');
      tempDiv.innerHTML = html;

      const headingElements = tempDiv.querySelectorAll('h2[id], span[id]');
      return Array.from(headingElements).map((heading) => ({
        id: heading.id,
        text: heading.textContent.trim(),
      }));
    }

    async function fetchMarkdown() {
      try {
        const serverUrl = import.meta.env.VITE_README_SERVER_URL || 'http://localhost:3001/readme';
        const response = await fetch(serverUrl);

        if (response.ok) {
          const rawMarkdown = await response.text();
          if (rawMarkdown.trim()) {
            return rawMarkdown;
          }
        }
        throw new Error('No content from server');
      } catch (e) {
        console.warn('Falling back to assets/README.md:', e);
        const fallbackResponse = await fetch(new URL('./assets/README.md', import.meta.url));
        if (fallbackResponse.ok) {
          return await fallbackResponse.text();
        } else {
          throw new Error('Failed to load fallback README.md');
        }
      }
    }

    onMounted(async () => {
      try {
        const rawMarkdown = await fetchMarkdown();
        markdownContent.value = rawMarkdown;

       const htmlContent = marked(rawMarkdown);
        parsedMarkdown.value = htmlContent;

        headerData.value = {
          name: extractData(rawMarkdown, /<h1 id="name">(.*?)<\/h1>/),
          title: extractData(rawMarkdown, /<p id="title">(.*?)<\/p>/),
          ['profile-image']: extractData(rawMarkdown, /<img id="profile-image" src="(.*?)"/),
          profileAlt: 'Profile Picture',
          phone: extractData(rawMarkdown, /<a id="phone" .*?>(.*?)<\/a>/),
          email: extractData(rawMarkdown, /<a id="email" .*?mailto:(.*?)".*?>.*?<\/a>/),
          ['linked-in']: extractData(rawMarkdown, /<a id="linked-in" .*?href="(.*?)".*?>.*?<\/a>/),
        };
      
        headings.value = extractHeadings(htmlContent);

      } catch (e) {
        console.error('Failed to fetch README content:', e);
        error.value = 'Failed to load README content.';
      } finally {
        isLoading.value = false;
      }
    });

    return {
      markdownContent,
      parsedMarkdown,
      headerData,
      headings,
      isLoading,
      error,
    };
  },
  data() {
    return {
      isMenuOpen: false,
    };
  },
  methods: {
    toggleMenu() {
      this.isMenuOpen = !this.isMenuOpen;
    },
    closeMenu() {
      this.isMenuOpen = false;
    },
  },
};
</script>

<style>
/* General Styles */
body {
  margin: 0;
  font-family: 'Roboto', Arial, sans-serif;
  background-color: #f4f4f9;
  color: #333;
  font-size: 16px; /* Base font size for rem calculations */
}

html {
  scroll-padding-top: 20rem; /* Adjusted for mobile devices */
}

#app {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* Add loader styles */
.loader {
  text-align: center;
  padding: 2rem;
  font-size: 1.25rem;
  color: #0073b1;
}

/* Skip Link Styles */
.skip-link {
  position: absolute;
  top: -2.5rem; /* Adjusted from -40px to -2.5rem */
  left: 0.625rem; /* Adjusted from 10px to 0.625rem */
  background: #005582;
  color: white;
  padding: 0.5rem 1rem; /* Adjusted from 8px 16px to 0.5rem 1rem */
  text-decoration: none;
  font-size: 1rem;
  z-index: 1000;
  border-radius: 0.25rem; /* Adjusted from 4px to 0.25rem */
  transition: top 0.3s ease;
}

.skip-link:focus {
  top: 0.625rem; /* Adjusted from 10px to 0.625rem */
  outline: 0.125rem solid #fff; /* Adjusted from 2px to 0.125rem */
}

/* Navigation Menu Styles */
.navigation-menu {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: linear-gradient(135deg, #0073b1, #005582);
  color: white;
  text-align: center;
  padding: 1.25rem; /* Adjusted from 20px to 1.25rem */
  box-shadow: 0 0.25rem 0.375rem rgba(0, 0, 0, 0.1); /* Adjusted from 0 4px 6px */
  z-index: 1001;
}

.navigation-menu ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  gap: 1.25rem; /* Adjusted from 20px to 1.25rem */
  transition: max-height 0.3s ease;
}

.navigation-menu ul.menu-open {
  display: flex;
  flex-direction: column;
}

.navigation-menu ul.desktop-menu {
  flex-direction: row;
}

.navigation-menu a {
  color: white;
  text-decoration: none;
  font-weight: bold;
}

.navigation-menu a:hover {
  text-decoration: underline;
}

.menu-toggle {
  display: none;
  background: none;
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
}

@media (max-width: 48rem) and (orientation: portrait) {
  .menu-toggle {
    display: block;
  }

  .navigation-menu ul.desktop-menu {
  display: none;
}

  .navigation-menu ul {
    flex-direction: column;
    gap: 0.625rem; /* Adjusted from 10px to 0.625rem */
  }
  html {
  scroll-padding-top: 5rem; /* Adjusted for mobile devices */
}
}

@media (min-width: 48.01rem) or (orientation: landscape) {
  .menu-toggle {
    display: none;
  }

  .navigation-menu ul {
    flex-direction: row;
    display: fixed;
  }
}

/* Main Content Styles */
.app-main {
  flex: 1;
  padding: 1.25rem; /* Adjusted from 20px to 1.25rem */
  max-width: 75rem; /* Adjusted from 1200px to 75rem */
  margin: 2.5rem auto; /* Adjusted from 40px to 2.5rem */
  background-color: #ffffff;
  border-radius: 0.5rem; /* Adjusted from 8px to 0.5rem */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.1); /* Adjusted from 0 4px 8px */
}

/* Footer Styles */
.app-footer {
  background-color: grey;
  color: white;
  text-align: center;
  padding: 1.25rem 0.625rem; /* Adjusted from 20px 10px to 1.25rem 0.625rem */
  margin-top: 1.25rem; /* Adjusted from 20px to 1.25rem */
}

.footer-content p {
  margin: 0.3125rem 0; /* Adjusted from 5px to 0.3125rem */
}

.footer-content a {
  color: white;
  text-decoration: none;
  font-weight: bold;
}

.footer-content a:hover {
  text-decoration: underline;
}

.header-container {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap; /* Ensures responsiveness */
}

.profile-image {
  width: 7.5rem; /* Converted from 120px */
  height: 7.5rem; /* Converted from 120px */
  border-radius: 50%;
  object-fit: cover;
  border: 0.25rem solid white; /* Converted from 4px */
  margin-right: 2.5rem; /* Converted from 40px */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.2); /* Converted from 0 4px 8px */
}

.profile-image:hover,
.profile-image:focus {
  transform: perspective(500px) rotateX(0deg) rotateY(0deg) scale(1.05);
  box-shadow: 0.9375rem 0.9375rem 1.875rem rgba(0, 0, 0, 0.5), -0.625rem -0.625rem 1.25rem rgba(255, 255, 255, 0.6); /* Converted from 15px, 30px, etc. */
  outline: none; /* Removes default focus outline */
}

.profile-image:focus {
  outline: 0.125rem solid #0073b1; /* Converted from 2px */
}

.header-content h1 {
  margin: 0;
  font-size: 2.5rem; /* Converted from 40px */
  font-weight: bold;
}

.header-content p {
  font-weight: bold;
  font-size: 1.5rem; /* Converted from 24px */
}

.resume {
  padding: 1.25rem; /* Converted from 20px */
  font-family: Arial, sans-serif;
  line-height: 1.6;
  max-width: 50rem; /* Converted from 800px */
  margin: 0 auto;
  background-color: #f9f9f9;
  border-radius: 0.5rem; /* Converted from 8px */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.1); /* Converted from 0 4px 8px */
}

.resume-header {
  position: sticky; /* Keeps the header sticky */
  top: 3.4375rem; /* Converted from 55px */
  background: linear-gradient(135deg, #0073b1, #005582); /* Blues for trust and professionalism */
  color: white;
  text-align: center;
  padding: 1.25rem; /* Converted from 20px */
  box-shadow: 0 0.25rem 0.375rem rgba(0, 0, 0, 0.1); /* Converted from 0 4px 6px */
  z-index: 1000;
  align-items: center;
  justify-content: center;
}

.contact-links {
  text-align: left;
}

.contact-link {
  color: white; /* Makes the anchor text white */
  text-decoration: none; /* Removes underline by default */
}

.contact-link:hover {
  text-decoration: underline; /* Adds underline on hover */
}

.resume-content {
  padding: 1.25rem; /* Converted from 20px */
  background: #fff;
  border-radius: 0.5rem; /* Converted from 8px */
  box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.1); /* Converted from 0 2px 4px */
}

@media (max-height: 48rem) {
  .resume-header {
    display: none;
  }
}

@media (max-width: 48rem) and (max-height: 48rem), (orientation: portrait) or (max-height: 48rem) { /* Target portrait mode on mobile devices */
  
  .resume-header {
    display: none; /* Hides the profile image */
  }
}


@media (max-width: 48rem) { /* Converted from 768px */
  .header-container {
    flex-direction: column;
    text-align: center;
  }

  .profile-image {
    margin-right: 0;
    margin-bottom: 0.625rem; /* Converted from 10px */
  }

  .left-margin {
    margin-left: 1.5rem; /* Converted from 24px */
  }
}
</style>