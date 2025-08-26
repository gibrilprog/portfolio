<template>
  <div>
    <header>
      <nav>
        <ul>
          <li v-for="link in navLinks" :key="link.href">
            <a :href="link.href">{{ link.text }}</a>
          </li>
        </ul>
      </nav>
    </header>

    <main>
      <!-- Section principale -->
      <div class="description-principale">
        <h1 class="main-title">{{ mainTitle }}</h1>
        <p>{{ education }}</p>
        <p class="stage-info">{{ stageInfo }}</p>
      </div>

      <!-- Sections dynamiques -->
      <section v-for="section in sections" :key="section.id" :id="section.id">
        <h2>{{ section.title }}</h2>

        <!-- Liste de contenus -->
        <ul v-if="section.items">
          <li v-for="(item, index) in section.items" :key="index">
            <template v-if="item.icon">
              <span :class="item.iconClass">{{ item.icon }}</span>
            </template>
            <div class="content">
              <h3 v-if="item.title">{{ item.title }}</h3>
              <p v-for="(subtitle, i) in item.subtitles" :key="i" class="subtitle">{{ subtitle }}</p>
            </div>
          </li>
        </ul>

        <!-- Liste simple (outils) -->
        <ul v-else-if="section.simpleList">
          <li v-for="(tool, i) in section.simpleList" :key="i">
            <div class="outils"><p class="outils-subtitle">{{ tool }}</p></div>
          </li>
        </ul>
      </section>
    </main>

    <footer>
      <p>&copy; 2025 Gibril BELHAIT. Tous droits réservés.</p>
    </footer>

    <div id="click-effects-container"></div>

    <div class="scroll-shapes">
      <div class="scroll-shape" v-for="n in 6" :key="n" :class="'shape' + n"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Navigation
const navLinks = ref([
  { href: "#about", text: "À propos" },
  { href: "#passions", text: "Passions" },
  { href: "#compétences", text: "Compétences" },
  { href: "#projets", text: "Projets" },
  { href: "#outils", text: "Outils" },
  { href: "#engagements", text: "Engagements" },
  { href: "#contact", text: "Contact" }
])

// Informations principales
const mainTitle = ref("Gibril BELHAIT")
const education = ref("Étudiant en 2ème année à EPITECH Mulhouse")
const stageInfo = ref("À la recherche d'un stage de 4 à 5 mois débutant en août")

// Sections dynamiques
const sections = ref([
  {
    id: "about",
    title: "À propos de moi",
    items: [
      { subtitles: ["Étudiant à Epitech Mulhouse, je développe mes compétences en programmation et explore le monde fascinant du développement logiciel, du DevOps et des jeux vidéo."] }
    ]
  },
  {
    id: "passions",
    title: "Mes passions",
    items: [
      { icon: "💻", iconClass: "passion-icon", title: "Informatique", subtitles: ["Développement web, programmation et nouvelles technologies"] },
      { icon: "🎹", iconClass: "passion-icon", title: "Music", subtitles: ["Jouer du piano dans mon temps libre"] },
      { icon: "🏊‍♂️", iconClass: "passion-icon", title: "Sport", subtitles: ["Natation", "Judo", "Aikido", "Karaté", "Taekwondo"] },
      { icon: "🎮", iconClass: "passion-icon", title: "Jeux Vidéos", subtitles: ["Développement de jeux vidéos stratégique.", "Développement pack de textures et modification de jeux (mods)."] }
    ]
  },
  {
    id: "compétences",
    title: "Mes compétences",
    items: [
      { icon: "⬛", iconClass: "compétences", title: "Langages acquis", subtitles: ["C", "Node.js", "React", "HTML/CSS", "Python"] },
      { icon: "📚", iconClass: "compétences", title: "Langages en cours", subtitles: ["Java"] },
      { icon: "🎯", iconClass: "compétences", title: "Objectif prochain langage", subtitles: ["C++"] }
    ]
  },
  {
    id: "projets",
    title: "Mes projets",
    items: [
      { icon: "👻", iconClass: "projets-icon", title: "Jeu d'horreur", subtitles: ["Développement d'un jeu d'horreur pendant la Game Jame 2025 d'Epitech ou le thème fut l'illusions. Avec une équipe de 6 membres nous avons travailler dessus pendant un week end sur le logiciel Unity"] },
      { icon: "🕹️", iconClass: "projets-icon", title: "Wolf3d", subtitles: ["Développement d'un FPS du style Wolfenstein et Ultrakill", "Utilisation de la 2d isométrique"] },
      { icon: "✈️", iconClass: "projets-icon", title: "Simulateur de contrôle aérien", subtitles: ["Simulateur fait en Csfml, qui contrôle les choques des avions dans la zone des tours de contrôle", "Baser sur le jeu vidéo League of Legends"] }
    ]
  },
  {
    id: "outils",
    title: "Outils",
    simpleList: ["Unity", "Github", "git", "Linux", "Docker"]
  },
  {
    id: "engagements",
    title: "Mes engagements",
    items: [
      { icon: "🚒", iconClass: "engagement-icon", title: "Jeune Sapeurs Pompiers", subtitles: ["3 ans de service à Rixheim (2018 - 2021)"], description: "Formation secourisme, exercices pratiques et esprit d'équipe" },
      { icon: "🏭", iconClass: "engagement-icon", title: "Mondelez/Suchard", subtitles: ["1 mois d'activité à Lörrach en Allemagne"], description: "Travail manuel, sens de l'organisation" }
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

// Scroll et clic
function handleScroll() {
  const scrollY = window.scrollY
  const factors = [-0.1, -0.08, -0.06, -0.12, -0.05, -0.09]
  document.querySelectorAll('.scroll-shape').forEach((el, i) => {
    el.style.transform = `translateY(${scrollY * factors[i]}px)`
  })
}

function createClickEffect(x, y) {
  const container = document.getElementById('click-effects-container')
  const ripple = document.createElement('div')
  ripple.className = 'click-ripple'
  ripple.style.left = x + 'px'
  ripple.style.top = y + 'px'
  container.appendChild(ripple)
  setTimeout(() => ripple.parentNode?.removeChild(ripple), 1000)
}

function handleClick(e) {
  createClickEffect(e.clientX, e.clientY)
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
  document.addEventListener('click', handleClick)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  document.removeEventListener('click', handleClick)
})
</script>

<style>
@import url('style/styles.css');
.main-title {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  color: #1e293b;
  font-weight: 700;
}
.subtitle {
  margin: 0.2rem 0;
}
</style>
