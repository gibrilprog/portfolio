<template>
  <div class="ds-app">
    <!-- ===================== TITLE SCREEN ===================== -->
    <transition name="title-fade">
      <div v-if="showTitleScreen" class="title-screen" @click="enterPortfolio" @keydown="enterPortfolio" tabindex="0">
        <div class="title-vignette"></div>

        <div class="title-content">
          <div class="title-logo">
            <div class="title-line-container">
              <div class="title-line"></div>
            </div>
            <h1 class="title-name">GIBRIL BELHAIT</h1>
            <div class="title-line-container">
              <div class="title-line"></div>
            </div>
          </div>

          <div class="title-prompt-box">
            <p class="title-prompt">APPUYER POUR CONTINUER</p>
          </div>
        </div>

        <div class="title-credits">
          <p>Portfolio &copy; 2026 Gibril BELHAIT &mdash; Étudiant en 2ème année à Epitech Mulhouse</p>
        </div>
      </div>
    </transition>

    <!-- ===================== PORTFOLIO ===================== -->
    <transition name="portfolio-fade">
      <div v-if="!showTitleScreen" class="portfolio">
        <!-- Background -->
        <div class="portfolio-bg"></div>
        <div class="portfolio-overlay"></div>

        <!-- Health bar (top-left) -->
        <div class="ds-health-bar">
          <div class="health-bar-name">Gibril BELHAIT</div>
          <div class="health-bar-img">
            <img src="/darksouls/dark_souls_health_bar.png" alt="HP" />
            <div class="health-bar-fill" :style="{ width: scrollPercent + '%' }"></div>
          </div>
        </div>

        <!-- DS3-style overlay panel -->
        <div class="ds-panel">
          <div class="ds-panel-inner">
            <!-- Panel navigation tabs -->
            <nav class="ds-panel-nav">
              <button
                v-for="link in navLinks"
                :key="link.id"
                class="ds-panel-tab"
                :class="{ active: activeSection === link.id }"
                @click="setActiveSection(link.id)"
              >{{ link.text }}</button>
            </nav>

            <div class="ds-panel-divider"></div>

            <!-- Hero info -->
            <div v-if="activeSection === 'about'" class="ds-panel-content">
              <div class="ds-hero-info">
                <h2 class="hero-title">Gibril BELHAIT</h2>
                <p class="hero-subtitle">Étudiant en 2ème année à Epitech Mulhouse</p>
                <p class="hero-search">À la recherche d'un part-time pour la 3ème année</p>
              </div>
              <div class="ds-panel-divider-thin"></div>
              <div class="ds-section-content">
                <p v-for="(sub, i) in getSection('about').items[0].subtitles" :key="i" class="ds-item-text">{{ sub }}</p>
              </div>
            </div>

            <!-- Regular sections with items -->
            <div v-else class="ds-panel-content">
              <template v-if="currentSection?.items">
                <div
                  v-for="(item, index) in currentSection.items"
                  :key="index"
                  class="ds-list-item"
                  :class="{ 'ds-list-item-selected': selectedItem === index }"
                  @click="selectedItem = index"
                >
                  <span v-if="item.icon" class="ds-icon">{{ item.icon }}</span>
                  <div class="ds-item-content">
                    <h3 v-if="item.title" class="ds-item-title">{{ item.title }}</h3>
                    <p v-for="(subtitle, i) in item.subtitles" :key="i" class="ds-item-text">{{ subtitle }}</p>
                  </div>
                </div>
              </template>

              <template v-else-if="currentSection?.simpleList">
                <div class="ds-list-simple">
                  <div
                    v-for="(tool, i) in currentSection.simpleList"
                    :key="i"
                    class="ds-tool-item"
                    :class="{ 'ds-tool-item-selected': selectedItem === i }"
                    @click="selectedItem = i"
                  >
                    <span class="ds-tool-text">{{ tool }}</span>
                  </div>
                </div>
              </template>
            </div>

            <!-- Bottom help bar -->
            <div class="ds-panel-bottom">
              <span class="ds-help-item">↑ ↓ Naviguer</span>
            </div>
          </div>
        </div>

        <footer class="ds-footer">
          <div class="footer-line"></div>
          <p>&copy; 2026 Gibril BELHAIT &mdash; Tous droits réservés</p>
        </footer>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const showTitleScreen = ref(true)
const activeSection = ref('about')
const selectedItem = ref(0)
const scrollPercent = ref(100)

function enterPortfolio() {
  showTitleScreen.value = false
}

function setActiveSection(id) {
  activeSection.value = id
  selectedItem.value = 0
}

function getSection(id) {
  return sections.value.find(s => s.id === id)
}

const currentSection = computed(() => {
  return sections.value.find(s => s.id === activeSection.value)
})

// Navigation
const navLinks = ref([
  { id: "about", text: "À propos" },
  { id: "passions", text: "Passions" },
  { id: "compétences", text: "Compétences" },
  { id: "projets", text: "Projets" },
  { id: "outils", text: "Outils" },
  { id: "engagements", text: "Engagements" },
  { id: "stages", text: "Stages" },
  { id: "contact", text: "Contact" }
])

// Sections
const sections = ref([
  {
    id: "about",
    title: "À propos de moi",
    items: [
      { subtitles: [
        "Étudiant en 2ème année à Epitech Mulhouse, je développe mes compétences en programmation et explore le monde du développement logiciel, du DevOps et des jeux vidéo.",
        "Actuellement à la recherche d'un part-time pour ma 3ème année."
      ] }
    ]
  },
  {
    id: "passions",
    title: "Mes passions",
    items: [
      { icon: "💻", title: "Informatique", subtitles: ["Développement web, programmation et nouvelles technologies"] },
      { icon: "🎹", title: "Musique", subtitles: ["Jouer du piano dans mon temps libre"] },
      { icon: "🏊‍♂️", title: "Sport", subtitles: ["Natation", "Judo", "Aikido", "Karaté", "Taekwondo"] },
      { icon: "🎮", title: "Jeux Vidéos", subtitles: ["Développement de jeux vidéos stratégique.", "Développement pack de textures et modification de jeux (mods)."] }
    ]
  },
  {
    id: "compétences",
    title: "Mes compétences",
    items: [
      { icon: "⚔️", title: "Langages acquis", subtitles: ["C", "PHP", "HTML/CSS", "Node.js", "React", "Python"] },
      { icon: "📚", title: "Langages en cours", subtitles: ["Java"] },
      { icon: "🎯", title: "Objectif prochain langage", subtitles: ["C++"] }
    ]
  },
  {
    id: "projets",
    title: "Mes projets",
    items: [
      { icon: "👻", title: "Jeu d'horreur", subtitles: ["Développement d'un jeu d'horreur pendant la Game Jam 2025 d'Epitech où le thème fut l'illusion. Avec une équipe de 6 membres nous avons travaillé dessus pendant un week-end sur Unity."] },
      { icon: "🕹️", title: "Wolf3d", subtitles: ["Développement d'un FPS du style Wolfenstein et Ultrakill", "Utilisation de la 2D isométrique"] },
      { icon: "✈️", title: "Simulateur de contrôle aérien", subtitles: ["Simulateur fait en CSFML, qui contrôle les chocs des avions dans la zone des tours de contrôle"] }
    ]
  },
  {
    id: "outils",
    title: "Outils",
    simpleList: ["Unity", "GitHub", "Git", "Linux", "Docker"]
  },
  {
    id: "engagements",
    title: "Mes engagements",
    items: [
      { icon: "🚒", title: "Jeune Sapeurs Pompiers", subtitles: ["3 ans de service à Rixheim (2018 - 2021)", "Formation secourisme, exercices pratiques et esprit d'équipe"] },
      { icon: "🏭", title: "Mondelez/Suchard", subtitles: ["1 mois d'activité à Lörrach en Allemagne", "Travail manuel, sens de l'organisation"] }
    ]
  },
  {
    id: "stages",
    title: "Expériences — Stages",
    items: [
      { icon: "🌐", title: "Stage Web — Développement Front & Back",
        subtitles: [
          "Développement web en PHP et HTML/CSS.",
          "Création et maintenance de sites web, intégration de maquettes et développement de fonctionnalités côté serveur."
        ]
      },
      { icon: "🌐", title: "Stage Web — Intégration & PHP",
        subtitles: [
          "Deuxième expérience en développement web avec PHP et HTML/CSS.",
          "Renforcement des compétences en intégration web et en programmation côté serveur."
        ]
      }
    ]
  },
  {
    id: "contact",
    title: "Contact",
    items: [
      { subtitles: ["✉️ gibril.belhait@epitech.eu", "📞 07 83 89 91 94"] }
    ]
  }
])

// Keyboard navigation
function handleKeydown(e) {
  if (showTitleScreen.value) return
  const section = currentSection.value
  const itemCount = section?.items?.length || section?.simpleList?.length || 0

  // Up/Down: navigate sub-items within the current category
  if (e.key === 'ArrowDown') {
    e.preventDefault()
    if (itemCount > 0) {
      selectedItem.value = (selectedItem.value + 1) % itemCount
    }
  } else if (e.key === 'ArrowUp') {
    e.preventDefault()
    if (itemCount > 0) {
      selectedItem.value = (selectedItem.value - 1 + itemCount) % itemCount
    }
  }
  // Left/Right: switch categories
  else if (e.key === 'ArrowRight') {
    e.preventDefault()
    const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
    const next = (idx + 1) % navLinks.value.length
    activeSection.value = navLinks.value[next].id
    selectedItem.value = 0
  } else if (e.key === 'ArrowLeft') {
    e.preventDefault()
    const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
    const prev = (idx - 1 + navLinks.value.length) % navLinks.value.length
    activeSection.value = navLinks.value[prev].id
    selectedItem.value = 0
  }
}

// Scroll percent for health bar (tracks mouse scroll on panel)
function handleScroll() {
  const maxScroll = document.documentElement.scrollHeight - window.innerHeight
  if (maxScroll > 0) {
    scrollPercent.value = Math.max(5, 100 - (window.scrollY / maxScroll) * 95)
  }
}

onMounted(() => {
  window.addEventListener('keydown', handleKeydown)
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
  window.removeEventListener('scroll', handleScroll)
})
</script>

<style>
@import url('style/styles.css');
</style>
