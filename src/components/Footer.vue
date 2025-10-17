<template>
    <footer ref="footerRef" class="site-footer">
        <div class="footer-inner">
            <div class="footer-left">
                <div class="brand">
                    <h3 class="brand-name">Sadowscy</h3>
                </div>

                <p class="maps-info">
                    Korzystanie z Map Google na tej stronie może wiązać się z zastosowaniem plików cookie przez Google.
                    Szczegóły znajdziesz w naszej
                    <a class="privacy-link" :href="privacyHref" target="_blank" rel="noopener">Polityce Prywatności</a>.
                </p>
            </div>

            <div class="footer-center">
                <nav class="socials" aria-label="Media społecznościowe">
                    <a
                        class="social social-fb"
                        href="https://www.facebook.com/"
                        target="_blank"
                        rel="noopener"
                        aria-label="Facebook"
                        ref="fbRef"
                    >
                        <!-- Facebook SVG -->
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
                            <path d="M22 12.07C22 6.48 17.52 2 11.93 2S1.86 6.48 1.86 12.07C1.86 17.08 5.86 21.18 10.64 21.98v-6.99H8.08v-2.93h2.56V9.41c0-2.53 1.5-3.92 3.79-3.92 1.1 0 2.25.2 2.25.2v2.47h-1.26c-1.24 0-1.62.77-1.62 1.56v1.87h2.76l-.44 2.93h-2.32V22C18.14 21.18 22 17.08 22 12.07z"/>
                        </svg>
                    </a>

                    <a
                        class="social social-ig"
                        href="https://www.instagram.com/"
                        target="_blank"
                        rel="noopener"
                        aria-label="Instagram"
                        ref="igRef"
                    >
                        <!-- Instagram SVG -->
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor" aria-hidden="true">
                            <path d="M7 2C4.243 2 2 4.243 2 7v10c0 2.757 2.243 5 5 5h10c2.757 0 5-2.243 5-5V7c0-2.757-2.243-5-5-5H7zm10 2c1.654 0 3 1.346 3 3v10c0 1.654-1.346 3-3 3H7c-1.654 0-3-1.346-3-3V7c0-1.654 1.346-3 3-3h10zM12 7.5a4.5 4.5 0 100 9 4.5 4.5 0 000-9zm0 2a2.5 2.5 0 110 5 2.5 2.5 0 010-5zM17.5 6a.9.9 0 11-1.8 0 .9.9 0 011.8 0z"/>
                        </svg>
                    </a>
                </nav>
            </div>

            <div class="footer-right">
                <p class="copyright">© 2025 Sadowscy | <a :href="privacyHref" class="privacy-link" target="_blank" rel="noopener">Polityka Prywatności</a></p>
            </div>
        </div>
    </footer>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const footerRef = ref(null)

// optional: a privacy link used in template (was referenced in original template)
const privacyHref = '/privacy' // adjust to your real URL

onMounted(() => {
    // Fade + slide the whole footer when it enters viewport
    gsap.from(footerRef.value, {
        y: 28,
        autoAlpha: 0,
        duration: 0.8,
        ease: 'power2.out',
        scrollTrigger: {
            trigger: footerRef.value,
            start: 'top bottom', // when top of footer hits bottom of viewport
            end: 'top center',
            toggleActions: 'play none none none'
        }
    })

    // Stagger appearance of social icons
    const socials = footerRef.value.querySelectorAll('.social')
    gsap.from(socials, {
        y: 12,
        autoAlpha: 0,
        stagger: 0.08,
        duration: 0.45,
        ease: 'power2.out',
        scrollTrigger: {
            trigger: footerRef.value,
            start: 'top bottom+=60',
            toggleActions: 'play none none none'
        }
    })

    // small hover scale on icons (adds a subtle pop on mount so hover feels snappier)
    gsap.set(socials, { transformOrigin: '50% 50%' })
})

onBeforeUnmount(() => {
    // clean up ScrollTrigger instances and tweens created by this component
    ScrollTrigger.getAll().forEach(t => t.kill())
    gsap.globalTimeline.clear()
})
</script>

<style scoped>
.site-footer {
    background: linear-gradient(180deg, #0f1724 0%, #081122 100%);
    color: #e6eef8;
    padding: 36px 20px;
    font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
}

.footer-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: flex;
    gap: 20px;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
}

.brand-name {
    margin: 0;
    font-size: 1.1rem;
    letter-spacing: 0.6px;
    color: #fff;
}

.maps-info {
    margin: 8px 0 0;
    font-size: 0.9rem;
    color: #cbd7e6;
    max-width: 520px;
    line-height: 1.4;
}

.privacy-link {
    color: #9fd2ff;
    text-decoration: underline;
}

.privacy-link:hover {
    color: #c9f0ff;
}

.socials {
    display: flex;
    gap: 12px;
    align-items: center;
}

.social {
    width: 44px;
    height: 44px;
    border-radius: 8px;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    color: #061526;
    background: #ffffff;
    text-decoration: none;
    transition: transform 0.18s ease, box-shadow 0.18s ease;
    box-shadow: 0 2px 8px rgba(0,0,0,0.15);
}
.social svg { display: block; }

.social:hover {
    transform: translateY(-4px) scale(1.03);
    box-shadow: 0 8px 18px rgba(0,0,0,0.25);
}

.social-fb { background: #1877F2; color: #fff; }
.social-ig { background: radial-gradient(circle at 30% 107%, #fdf497 0%, #fdf497 5%, #fd5949 45%, #d6249f 60%, #285AEB 90%); color: #fff; }

.footer-right { text-align: right; min-width: 200px; }
.footer-right p { margin: 0; font-size: 0.95rem; color: #cbd7e6; }

@media (max-width: 720px) {
    .footer-inner { flex-direction: column; align-items: flex-start; gap: 14px; }
    .footer-right { width: 100%; text-align: left; }
    .footer-center { order: 3; width: 100%; display:flex; justify-content:flex-start; }
}
</style>