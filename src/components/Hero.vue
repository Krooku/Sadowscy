<template>
    <section id="hero" class="hero" ref="hero">
        <div class="bg" ref="bg">
            <!-- Podmień ścieżkę na własny obraz w /src/assets lub użyj zewnętrznego URL -->
            <img src="@/assets/bg_img.jpg" alt="tło warsztatu samochodowego" />
        </div>

        <div class="overlay"></div>

        <div class="content" ref="content">
            <h1 class="logo">Sadowscy</h1>
            <p class="tag">AUTO - CZĘŚCI - SZYBY - NAPRAWA</p>

            <h2>Naprawa szyb samochodowych</h2>
            <p class="desc">
                Specjalizujemy się w naprawie i wymianie szyb czołowych oraz bocznych. Szybka diagnoza,
                profesjonalny montaż i gwarancja szczelności. Obsługujemy wszystkie marki samochodów.
            </p>

            <button class="cta">Skontaktuj się</button>
        </div>
    </section>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger)

export default {
  name: 'Hero',
  setup() {
    const hero = ref(null)
    const bg = ref(null)
    const content = ref(null)

    onMounted(() => {
      // Parallax tła
      gsap.to(bg.value, {
        yPercent: 100,
        ease: 'none',
        scrollTrigger: {
          trigger: hero.value,
          start: 'top top',
          end: 'bottom top',
          scrub: true
        }
      })

      // Powiększenie tła na starcie
      gsap.fromTo(bg.value, { scale: 1.06 }, { scale: 1, duration: 8, ease: 'power1.out' })

      // Animacja zawartości
      const children = Array.from(content.value.querySelectorAll('.logo, .tag, h2, .desc, .cta'))
      
      const tl = gsap.timeline({
        scrollTrigger: {
          trigger: hero.value,
          start: 'top center',
          toggleActions: 'play none none none'
        }
      })

      // Fade + slide content
      tl.fromTo(content.value, 
        { y: 18, autoAlpha: 0 },
        { y: 0, autoAlpha: 1, duration: 0.6, ease: 'power2.out' }
      )

      // Stagger dzieci
      tl.fromTo(children, 
        { y: 12, autoAlpha: 0 },
        { y: 0, autoAlpha: 1, stagger: 0.12, duration: 0.5, ease: 'power2.out' },
        '-=0.35'
      )
    })

    onBeforeUnmount(() => {
      ScrollTrigger.getAll().forEach(t => t.kill())
    })

    return { hero, bg, content }
  }
}

</script>

<style scoped>
.hero {
    position: relative;
    width: 100%;
    height: 100vh;
    overflow: hidden; /* allow bg to bleed outside so next section can cover it */
    display: flex;
    align-items: center;
    justify-content: center;
    color: #fff;
    box-sizing: border-box;
}

/* warstwa tła - obraz */
.bg {
    position: absolute;
    inset: 0;
    will-change: transform;
    z-index: -1; /* niżej od treści */
}
.bg img {
    width: 100%;
    height: 105%;
    object-fit: cover;
    display: block;
    transform-origin: center top;
}

/* przyciemnienie warstwy dla kontrastu tekstu */
.overlay {
    position: absolute;
    inset: 0;
    background: linear-gradient(40deg, rgba(0,0,0,0.65), rgba(0,0,0,0.2));
    pointer-events: none;
    z-index: 2;
}

/* zawartość */
.content {
    position: relative;
    z-index: 3; /* nad tłem i nakładką */
    text-align: center;
    padding: 2rem;
    max-width: 1000px;
    opacity: 1; /* widoczne domyślnie — GSAP robi animację from(0) -> 1 */
    will-change: transform, opacity;
}
.logo, .tag, h2, .desc, .cta {
  will-change: transform, opacity;
}
.logo {
    font-size: 64px;
    margin: 0;
    letter-spacing: 2px;
    font-weight: 700;
}
.tag {
    margin: 6px 0 18px;
    font-weight: 900;
    color: var(--color-secondary);
    letter-spacing: 1px;
}
h2 {
    margin: 0 0 10px;
    font-size: 28px;
}
.desc {
    margin: 0 auto 20px;
    max-width: 720px;
    line-height: 1.4;
    font-size: 16px;
    color: #f0f0f0;
}
.cta {
    background: var(--color-secondary);
    color: white;
    border: none;
    padding: 12px 22px;
    border-radius: 6px;
    cursor: pointer;
    font-weight: 700;
    box-shadow: 0 6px 18px rgba(0,0,0,0.25);
}

/* responsywność */
@media (max-width: 720px) {
    .logo { font-size: 32px; }
    h2 { font-size: 20px; }
    .desc { font-size: 15px; padding: 0 6px; }
}
</style>