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

          <transition name="prompt-fade">
            <div v-if="!titleClicked" class="title-prompt-box">
              <p class="title-prompt">APPUYER POUR CONTINUER</p>
            </div>
          </transition>
        </div>

        <div class="title-credits">
          <p>Portfolio &copy; 2026 Gibril BELHAIT &mdash; {{ t('Étudiant en 2ème année à Epitech Mulhouse', '2nd year student at Epitech Mulhouse') }}</p>
        </div>
      </div>
    </transition>

    <!-- ===================== PORTFOLIO ===================== -->
    <transition name="portfolio-fade">
      <div v-if="!showTitleScreen" class="portfolio">
        <!-- Background slideshow -->
        <div class="portfolio-bg portfolio-bg-back" :style="{ backgroundImage: 'url(' + bgImages[bgIndexBack] + ')' }"></div>
        <div class="portfolio-bg portfolio-bg-front" :class="{ 'bg-visible': bgFrontVisible }" :style="{ backgroundImage: 'url(' + bgImages[bgIndexFront] + ')' }"></div>
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
          <div class="ds-panel-frame">
            <!-- Ornate corner decorations -->
            <div class="frame-corner frame-tl"></div>
            <div class="frame-corner frame-tr"></div>
            <div class="frame-corner frame-bl"></div>
            <div class="frame-corner frame-br"></div>

            <div class="ds-panel-inner">
              <!-- Category title -->
              <div class="ds-category-title">{{ activeSection === 'parametres' ? t('Paramètres', 'Settings') : (currentSection?.title || t('À propos de moi', 'About me')) }}</div>

              <!-- Tab row with LB / RB -->
              <div class="ds-tab-row">
                <span class="ds-tab-arrow" @click="prevTab">&lt;LB</span>
                <nav class="ds-panel-nav">
                  <button
                    v-for="link in navLinks"
                    :key="link.id"
                    class="ds-panel-tab"
                    :class="{ active: activeSection === link.id }"
                    @click="setActiveSection(link.id)"
                  >{{ link.text }}</button>
                </nav>
                <span class="ds-tab-arrow" @click="nextTab">RB&gt;</span>
              </div>

              <div class="ds-panel-divider"></div>

              <!-- Hero info (About) -->
              <div v-if="activeSection === 'about'" class="ds-panel-content">
                <div class="ds-content-row">
                  <span class="ds-row-label">{{ t('Nom', 'Name') }}</span>
                  <span class="ds-row-value">Gibril BELHAIT</span>
                </div>
                <div class="ds-content-line"></div>
                <div class="ds-content-row">
                  <span class="ds-row-label">{{ t('Formation', 'Education') }}</span>
                  <span class="ds-row-value">{{ t('Epitech Mulhouse — Programme Grande Ecole - 2ème année', 'Epitech Mulhouse — Grand School Program - 2nd year') }}</span>
                </div>
                <div class="ds-content-line"></div>
                <div class="ds-content-row">
                  <span class="ds-row-label">{{ t('Recherche', 'Looking for') }}</span>
                  <span class="ds-row-value">{{ t('Part-time pour la 3ème année', 'Part-time for 3rd year') }}</span>
                </div>
                <div class="ds-content-line"></div>
                <div class="ds-content-row ds-content-row-desc">
                  <p v-for="(sub, i) in getSection('about').items[0].subtitles" :key="i" class="ds-row-desc">{{ sub }}</p>
                </div>
              </div>

              <!-- Settings (Paramètres) -->
              <div v-else-if="activeSection === 'parametres'" class="ds-panel-content">
                <div
                  class="ds-content-row ds-setting-row"
                  :class="{ 'ds-row-selected': selectedItem === 0 }"
                  @click="selectedItem = 0; playEnterSound(); toggleLang()"
                >
                  <span class="ds-row-label">{{ t('Langue', 'Language') }}</span>
                  <span class="ds-row-value ds-setting-value">{{ lang === 'fr' ? 'Français' : 'English' }}</span>
                </div>
                <div class="ds-content-line"></div>
              </div>

              <!-- Regular sections with items -->
              <div v-else class="ds-panel-content">
                <template v-if="currentSection?.items">
                  <template v-for="(item, index) in currentSection.items" :key="index">
                    <div
                      class="ds-content-row ds-content-row-clickable"
                      :class="{ 'ds-row-selected': selectedItem === index }"
                      @click="selectedItem = index"
                    >
                      <span v-if="item.icon" class="ds-row-icon">{{ item.icon }}</span>
                      <div class="ds-row-body">
                        <span v-if="item.title" class="ds-row-label">{{ item.title }}</span>
                        <span v-for="(subtitle, i) in item.subtitles" :key="i" class="ds-row-value-sub">{{ subtitle }}</span>
                      </div>
                    </div>
                    <div v-if="index < currentSection.items.length - 1" class="ds-content-line"></div>
                  </template>
                </template>

                <template v-else-if="currentSection?.simpleList">
                  <template v-for="(tool, i) in currentSection.simpleList" :key="i">
                    <div
                      class="ds-content-row ds-content-row-clickable"
                      :class="{ 'ds-row-selected': selectedItem === i }"
                      @click="selectedItem = i"
                    >
                      <span class="ds-row-label">{{ tool }}</span>
                    </div>
                    <div v-if="i < currentSection.simpleList.length - 1" class="ds-content-line"></div>
                  </template>
                </template>
              </div>

              <!-- Bottom help bar -->
              <div class="ds-panel-bottom">
                <span class="ds-help-item"><span class="ds-key">↑↓</span> {{ t('Naviguer', 'Navigate') }}</span>
                <span class="ds-help-item"><span class="ds-key">←→</span> {{ t('Catégorie', 'Category') }}</span>
                <span class="ds-help-item"><span class="ds-key">Entrée</span> {{ t('Confirmer', 'Confirm') }}</span>
              </div>
            </div>
          </div>
        </div>

        <footer class="ds-footer">
          <div class="footer-line"></div>
          <p>&copy; 2026 Gibril BELHAIT &mdash; {{ t('Tous droits réservés', 'All rights reserved') }}</p>
        </footer>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const showTitleScreen = ref(true)
const titleClicked = ref(false)
const activeSection = ref('about')
const selectedItem = ref(0)
const scrollPercent = ref(100)
const lang = ref('fr')

// Background slideshow
function shuffleArray(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
}

let bgImages = [
  '/darksouls/darksouls_watch_caste.png',
  '/darksouls/dark_souls_nameless_king.png',
  '/darksouls/dark_souls_vordt.png',
  '/darksouls/dark_souls_midir.png',
  '/darksouls/dark_souls_friede.png',
  '/darksouls/dark_souls_soul_of_cinder.png',
  '/darksouls/dark_souls_dancer.png',
  '/darksouls/dark_souls_abyss_watcher.png',
  '/darksouls/dark_souls_gael.png',
  '/darksouls/dark_souls_gundyr.png',
  '/darksouls/dark_souls_yhorm_clone.png'
]
shuffleArray(bgImages)
const bgIndexBack = ref(0)
const bgIndexFront = ref(1)
const bgFrontVisible = ref(false)
let bgInterval = null
let bgShowingFront = false

function cycleBg() {
  // Shuffle every full cycle
  if ((bgIndexFront.value + 1) % bgImages.length === 0 || (bgIndexBack.value + 1) % bgImages.length === 0) {
    shuffleArray(bgImages)
    bgIndexBack.value = 0
    bgIndexFront.value = 1
    bgFrontVisible.value = false
    bgShowingFront = false
    return
  }
  if (bgShowingFront) {
    // Front is visible, update back to next image, then fade to back
    bgIndexBack.value = (bgIndexFront.value + 1) % bgImages.length
    bgFrontVisible.value = false
  } else {
    // Back is visible, update front to next image, then fade to front
    bgIndexFront.value = (bgIndexBack.value + 1) % bgImages.length
    bgFrontVisible.value = true
  }
  bgShowingFront = !bgShowingFront
}

function toggleLang() {
  lang.value = lang.value === 'fr' ? 'en' : 'fr'
}

function t(fr, en) {
  return lang.value === 'fr' ? fr : en
}


// DS3 bonfire sound (title screen only) — skip 0.5s silence at start
const bonfireSound = new Audio('/sound_dark_souls/Bonfire Dark Souls Sound Effect.mp3')
bonfireSound.volume = 0.5

// DS3 confirm sound for Enter actions
const enterSound = new Audio('/sound_dark_souls/parametre_enter.mp3')
enterSound.volume = 0.5

// DS3 slide sound for arrow navigation
const slideSound = new Audio('/sound_dark_souls/slide_categories.mp3')
slideSound.volume = 0.5

// DS3 OST ambiance (starts after fade)
const ost = new Audio('/sound_dark_souls/dark_souls_ost.mp3')
ost.loop = true
ost.volume = 0

function fadeInOST(duration = 3000) {
  ost.volume = 0
  ost.play()
  const target = 0.10
  const steps = 30
  let step = 0
  const interval = setInterval(() => {
    step++
    ost.volume = Math.min(target, (step / steps) * target)
    if (step >= steps) clearInterval(interval)
  }, duration / steps)
}

function playEnterSound() {
  enterSound.currentTime = 0
  enterSound.play()
}

function playMenuClick() {
  slideSound.currentTime = 0.15
  slideSound.play()
}

function enterPortfolio() {
  if (titleClicked.value) return
  titleClicked.value = true
  bonfireSound.currentTime = 0.25
  bonfireSound.play()
  setTimeout(() => {
    showTitleScreen.value = false
    // Attend la fin du fade noir avant de lancer l'OST
    setTimeout(() => {
      fadeInOST(3000)
    }, 800) // doit matcher la durée du fade-out du titre
  }, 600)
}

function setActiveSection(id) {
  playMenuClick()
  activeSection.value = id
  selectedItem.value = 0
  // Scroll l’onglet actif dans la vue, en anticipant le sens
  setTimeout(() => {
    const nav = document.querySelector('.ds-panel-nav');
    const activeTab = nav?.querySelector('.ds-panel-tab.active');
    if (activeTab && nav) {
      const navRect = nav.getBoundingClientRect();
      const tabRect = activeTab.getBoundingClientRect();
      // Si l’onglet est trop à gauche, scroll vers la gauche
      if (tabRect.left < navRect.left) {
        nav.scrollBy({ left: tabRect.left - navRect.left - 40, behavior: 'smooth' });
      }
      // Si l’onglet est trop à droite, scroll vers la droite
      else if (tabRect.right > navRect.right) {
        nav.scrollBy({ left: tabRect.right - navRect.right + 40, behavior: 'smooth' });
      }
    }
  }, 0);
}

function prevTab() {
  const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
  const prev = (idx - 1 + navLinks.value.length) % navLinks.value.length
  setActiveSection(navLinks.value[prev].id)
}

function nextTab() {
  const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
  const next = (idx + 1) % navLinks.value.length
  setActiveSection(navLinks.value[next].id)
}

function getSection(id) {
  return sections.value.find(s => s.id === id)
}

const currentSection = computed(() => {
  return sections.value.find(s => s.id === activeSection.value)
})

// Navigation
const navLinks = computed(() => [
  { id: "about", text: t("À propos", "About") },
  { id: "passions", text: "Passions" },
  { id: "compétences", text: t("Compétences", "Skills") },
  { id: "projets", text: t("Projets", "Projects") },
  { id: "outils", text: t("Outils", "Tools") },
  { id: "engagements", text: t("Engagements", "Commitments") },
  { id: "stages", text: t("Stages", "Internships") },
  { id: "contact", text: "Contact" },
  { id: "parametres", text: t("Paramètres", "Settings") }
])

// Sections
const sections = computed(() => [
  {
    id: "about",
    title: t("À propos de moi", "About me"),
    items: [
      { subtitles: [
        t(
          "Étudiant en 2ème année à Epitech Mulhouse, je développe mes compétences en programmation et explore le monde du développement logiciel, du DevOps et des jeux vidéo.",
          "2nd year student at Epitech Mulhouse, developing my programming skills and exploring software development, DevOps and video games."
        ),
        t(
          "Actuellement à la recherche d'un part-time pour ma 3ème année.",
          "Currently looking for a part-time position for my 3rd year."
        )
      ] }
    ]
  },
  {
    id: "passions",
    title: t("Mes passions", "My passions"),
    items: [
      { icon: "💻", title: t("Informatique", "Computer Science"), subtitles: [t("Développement web, programmation et nouvelles technologies", "Web development, programming and new technologies")] },
      { icon: "🎹", title: t("Musique", "Music"), subtitles: [t("Jouer du piano dans mon temps libre", "Playing piano in my free time")] },
      { icon: "🏊‍♂️", title: "Sport", subtitles: [t("Natation", "Swimming"), "Judo", "Aikido", t("Karaté", "Karate"), "Taekwondo"] },
      { icon: "🎮", title: t("Jeux Vidéos", "Video Games"), subtitles: [t("Développement de jeux vidéos stratégique.", "Strategic video game development."), t("Développement pack de textures et modification de jeux (mods).", "Texture pack development and game modding.")] }
    ]
  },
  {
    id: "compétences",
    title: t("Mes compétences", "My skills"),
    items: [
      { icon: "⚔️", title: t("Langages acquis", "Acquired languages"), subtitles: ["C", "PHP", "HTML/CSS", "Node.js", "React", "Python"] },
      { icon: "📚", title: t("Langages en cours", "Languages in progress"), subtitles: ["Java"] },
      { icon: "🎯", title: t("Objectif prochain langage", "Next language goal"), subtitles: ["C++"] }
    ]
  },
  {
    id: "projets",
    title: t("Mes projets", "My projects"),
    items: [
      { icon: "👻", title: t("Jeu d'horreur", "Horror game"), subtitles: [t("Développement d'un jeu d'horreur pendant la Game Jam 2025 d'Epitech où le thème fut l'illusion. Avec une équipe de 6 membres nous avons travaillé dessus pendant un week-end sur Unity.", "Developed a horror game during Epitech's 2025 Game Jam with the theme of illusion. With a team of 6, we worked on it over a weekend using Unity.")] },
      { icon: "🕹️", title: "Wolf3d", subtitles: [t("Développement d'un FPS du style Wolfenstein et Ultrakill", "Developed a Wolfenstein/Ultrakill-style FPS"), t("Utilisation de la 2D isométrique", "Using isometric 2D")] },
      { icon: "✈️", title: t("Simulateur de contrôle aérien", "Air traffic control simulator"), subtitles: [t("Simulateur fait en CSFML, qui contrôle les chocs des avions dans la zone des tours de contrôle", "Simulator made in CSFML, managing aircraft collisions in control tower zones")] }
    ]
  },
  {
    id: "outils",
    title: t("Outils", "Tools"),
    simpleList: ["Unity", "GitHub", "Git", "Linux", "Docker"]
  },
  {
    id: "engagements",
    title: t("Mes engagements", "My commitments"),
    items: [
      { icon: "🚒", title: t("Jeune Sapeurs Pompiers", "Young Firefighters"), subtitles: [t("3 ans de service à Rixheim (2018 - 2021)", "3 years of service in Rixheim (2018 - 2021)"), t("Formation secourisme, exercices pratiques et esprit d'équipe", "First aid training, practical exercises and teamwork")] },
      { icon: "🏭", title: "Mondelez/Suchard", subtitles: [t("1 mois d'activité à Lörrach en Allemagne", "1 month of work in Lörrach, Germany"), t("Travail manuel, sens de l'organisation", "Manual labor, organizational skills")] }
    ]
  },
  {
    id: "stages",
    title: t("Expériences — Stages", "Experience — Internships"),
    items: [
      { icon: "🌐", title: t("Stage Web — Développement Front & Back", "Web Internship — Front & Back Development"),
        subtitles: [
          t("Développement web en PHP et HTML/CSS.", "Web development with PHP and HTML/CSS."),
          t("Création et maintenance de sites web, intégration de maquettes et développement de fonctionnalités côté serveur.", "Website creation and maintenance, mockup integration and server-side feature development.")
        ]
      },
      { icon: "🌐", title: t("Stage Web — Intégration & PHP", "Web Internship — Integration & PHP"),
        subtitles: [
          t("Deuxième expérience en développement web avec PHP et HTML/CSS.", "Second web development experience with PHP and HTML/CSS."),
          t("Renforcement des compétences en intégration web et en programmation côté serveur.", "Strengthened web integration and server-side programming skills.")
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
    playMenuClick()
    if (itemCount > 0) {
      selectedItem.value = (selectedItem.value + 1) % itemCount
    }
  } else if (e.key === 'ArrowUp') {
    e.preventDefault()
    playMenuClick()
    if (itemCount > 0) {
      selectedItem.value = (selectedItem.value - 1 + itemCount) % itemCount
    }
  }
  // Left/Right: switch categories
  else if (e.key === 'ArrowRight') {
    e.preventDefault()
    playMenuClick()
    const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
    const next = (idx + 1) % navLinks.value.length
    activeSection.value = navLinks.value[next].id
    selectedItem.value = 0
  } else if (e.key === 'ArrowLeft') {
    e.preventDefault()
    playMenuClick()
    const idx = navLinks.value.findIndex(l => l.id === activeSection.value)
    const prev = (idx - 1 + navLinks.value.length) % navLinks.value.length
    activeSection.value = navLinks.value[prev].id
    selectedItem.value = 0
  }
  // Enter: activate selected item
  else if (e.key === 'Enter') {
    e.preventDefault()
    if (activeSection.value === 'parametres' && selectedItem.value === 0) {
      playEnterSound()
      toggleLang()
    }
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
  bgInterval = setInterval(cycleBg, 8000)
})

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeydown)
  window.removeEventListener('scroll', handleScroll)
  if (bgInterval) clearInterval(bgInterval)
})
</script>

<style>
@import url('style/styles.css');
</style>
