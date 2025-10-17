<script setup>
import { defineAsyncComponent } from 'vue'
import NavBar from './components/NavBar.vue'
import Hero from './components/Hero.vue'

// Lazy-loaded components
const Services = defineAsyncComponent(() => import('./components/Services.vue'))
const Contact = defineAsyncComponent(() => import('./components/Contact.vue'))
const Footer = defineAsyncComponent(() => import('./components/Footer.vue'))
</script>

<template>
  <NavBar>
    <template #logo>
      <h2>Sadowscy</h2>
    </template>
  </NavBar>
  <Hero />
  <!-- Lazy components -->
  <Services />
  <Contact />
  <Footer />
</template>


<style scoped></style>
