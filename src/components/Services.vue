<template>
    <section id="services" class="services">
        <h2>Nasze Usługi</h2>

        <div class="service-list">
            <div
                class="service-card"
                v-for="(s, i) in services"
                :key="i"
                :data-index="i"
            >
                <div class="card-media">
                    <img :src="s.img" :alt="s.title" loading="lazy" />
                    <span class="media-badge" v-if="s.tag">{{ s.tag }}</span>
                </div>

                <div class="card-body">
                    <h3>{{ s.title }}</h3>
                    <p class="desc">{{ s.text }}</p>

                    <div class="card-footer">
                        <small class="meta" v-if="s.meta">{{ s.meta }}</small>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger);

import windscreen from "@/assets/services/windshield.jpeg";
import replace_windshield from "@/assets/services/replace_windshield.jpeg";
import mechanic from "@/assets/services/repair.jpeg";
import parts from "@/assets/services/car_parts.jpeg";
import van from "@/assets/services/van.jpeg";
import tug from "@/assets/services/tug.jpg";

export default {
    name: "Services",
    data() {
        return {
            services: [
                {
                    title: "Wymiana szyby czołowej",
                    text:
                        "Profesjonalna wymiana szyb — oryginalne i dedykowane zamienniki, montaż z użyciem wysokiej jakości materiałów.",
                    img: replace_windshield,
                    tag: "Szyby",
                    meta: "Gwarancja montażu"
                },
                {
                    title: "Naprawa szyby czołowej",
                    text:
                        "Szybkie naprawy odprysków i pęknięć — metoda umożliwiająca uniknięcie pełnej wymiany w wielu przypadkach.",
                    img: windscreen,
                    tag: "Naprawa",
                    meta: "Szybko i tanio"
                },
                {
                    title: "Ogólne naprawy samochodów",
                    text:
                        "Diagnostyka, mechanika, elektronika — kompleksowe naprawy wykonywane przez wykwalifikowany zespół.",
                    img: mechanic,
                    tag: "Mechanika",
                    meta: "Serwis pełen profesjonalizmu"
                },
                {
                    title: "Sprowadzanie i sprzedaż części",
                    text:
                        "Pomagamy w sprowadzeniu części oryginalnych i sprawdzonych zamienników. Oferujemy wsparcie przy wyborze.",
                    img: parts,
                    tag: "Części",
                    meta: "Oryginalne i używane"
                },
                {
                    title: "Wypożyczalnia busów",
                    text:
                        "Wynajem busów krótkoterminowy i długoterminowy — ubezpieczone i sprawne pojazdy, elastyczne warunki.",
                    img: van,
                    tag: "Wynajem",
                    meta: "Dostępne od ręki"
                },
                {
                    title: "Wypożyczalnia lawet",
                    text:
                        "Lawety z obsługą lub bez — idealne do transportu uszkodzonych pojazdów i sprzętu.",
                    img: tug,
                    tag: "Transport",
                    meta: "Pomoc drogowa"
                }
            ],
            _tl: null,
            _cardCleanups: [],
            _triggers: [] // store tweens/scrollTriggers to clean up
        };
    },
    mounted() {
        const root = this.$el;

        // Reveal heading + cards with a single timeline and stagger
        const cards = gsap.utils.toArray(root.querySelectorAll(".service-card"));
        const imgs = gsap.utils.toArray(root.querySelectorAll(".service-card img"));

        const tl = gsap.timeline({
            scrollTrigger: {
                trigger: root.querySelector(".service-list") || root,
                start: "top 80%",
                toggleActions: "play none none none",
                markers: true
            }
        });

        // heading
        tl.from(root.querySelector("h2"), {
            y: 30,
            opacity: 0,
            duration: 0.6,
            ease: "power3.out"
        });

        // cards slide+fade with stagger
        tl.fromTo(cards, {
            x: 260,
            opacity: 0
        }, {
            x: 0,
            opacity: 1,
            duration: 0.6,
            ease: "power3.out",
            stagger: 0.22
        }, "-=0.25");

        // subtle image reveal (scale + fade) in sync with card stagger
        // start slightly earlier so image anim feels nested
        tl.from(imgs, {
            scale: 0.98,
            opacity: 0,
            duration: 1.8,
            ease: "power3.out",
            stagger: 0.22
        }, "<"); // align with the previous stagger

        // store timeline for cleanup
        this._tl = tl;
        this._triggers.push(tl);

        // hover animations: scale card + subtle image zoom (unchanged)
        cards.forEach(card => {
            const img = card.querySelector("img");
            const enter = () => {
                gsap.to(card, { scale: 1.025, duration: 0.28, ease: "power1.out" });
                if (img) gsap.to(img, { scale: 1.08, duration: 0.6, ease: "power2.out" });
            };
            const leave = () => {
                gsap.to(card, { scale: 1, duration: 0.28, ease: "power1.out" });
                if (img) gsap.to(img, { scale: 1, duration: 0.6, ease: "power2.out" });
            };
            card.addEventListener("mouseenter", enter);
            card.addEventListener("mouseleave", leave);

            this._cardCleanups.push(() => {
                card.removeEventListener("mouseenter", enter);
                card.removeEventListener("mouseleave", leave);
                try { gsap.killTweensOf(card); } catch (e) {}
                if (img) try { gsap.killTweensOf(img); } catch (e) {}
            });
        });
    },
    beforeUnmount() {
        // cleanup listeners
        this._cardCleanups.forEach(fn => fn && fn());

        // kill stored tweens/scrollTriggers
        this._triggers.forEach(tw => {
            try {
                if (tw && tw.scrollTrigger) tw.scrollTrigger.kill();
            } catch (e) {}
            try { if (tw && tw.kill) tw.kill(); } catch (e) {}
        });
        this._triggers = [];

        // kill timeline & scrolltrigger safely
        if (this._tl) {
            try { if (this._tl.scrollTrigger) this._tl.scrollTrigger.kill(); } catch (e) {}
            try { this._tl.kill(); } catch (e) {}
            this._tl = null;
        }
        // defensive: kill any remaining ScrollTriggers
        try { ScrollTrigger.getAll().forEach(st => st.kill && st.kill()); } catch (e) {}
    }
};
</script>

<style scoped>
:root {
    --bg: #f6f7fb;
    --card-bg: #ffffff;
    --accent: linear-gradient(135deg,#2b9df4 0%, #7b6cff 100%);
    --text: #222;
    --muted: #6b7280;
}

.services {
    padding: 3rem 1.25rem;
    background: var(--bg);
    color: var(--text);
}

h2 {
    text-align: center;
    margin-bottom: 2rem;
    font-weight: 700;
    letter-spacing: -0.4px;
    font-size: 1.6rem;
}

.service-list {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1.5rem;
    max-width: 1200px;
    margin: 0 auto;
    align-items: stretch;
    overflow: hidden;
    padding: 0.5rem;
}

/* Responsive */
@media (max-width: 992px) {
    .service-list { grid-template-columns: repeat(2, 1fr); }
}
@media (max-width: 640px) {
    .service-list { grid-template-columns: 1fr; }
}

.service-card {
    background: var(--card-bg);
    border-radius: 12px;
    overflow: hidden;
    transform-origin: center;
    display: flex;
    flex-direction: column;
    cursor: default;
    min-height: 260px;
}

/* media */
.card-media {
    position: relative;
    width: 100%;
    aspect-ratio: 16/9;
    overflow: hidden;
    background: linear-gradient(180deg, #f0f3ff 0%, #ffffff 100%);
}
.card-media img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
    transition: transform 0.6s cubic-bezier(.2,.8,.2,1);
    transform-origin: center;
}

/* badge on image */
.media-badge {
    position: absolute;
    left: 12px;
    top: 12px;
    background: rgba(255,255,255,0.9);
    color: var(--text);
    padding: 6px 10px;
    border-radius: 999px;
    font-size: 0.8rem;
    font-weight: 600;
}

/* content */
.card-body {
    padding: 1rem 1rem 1.15rem 1rem;
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
    flex: 1 1 auto;
}
h3 {
    margin: 0;
    font-size: 1.05rem;
    font-weight: 700;
}
.desc {
    color: var(--muted);
    margin: 0;
    font-size: 0.95rem;
    line-height: 1.35;
}

/* footer */
.card-footer {
    margin-top: auto;
    align-self: flex-end;
}
.meta {
    color: var(--muted);
    font-size: 0.82rem;
    font-weight: 500;
}

/* button */
.btn {
    background: var(--accent);
    color: black;
    border: none;
    padding: 0.5rem 0.9rem;
    border-radius: 8px;
    font-weight: 700;
    cursor: pointer;
    transition: transform 0.18s ease, box-shadow 0.18s ease, filter 0.18s ease;
}
.btn:active { transform: translateY(1px) scale(0.998); }
.btn:hover { filter: brightness(1.02); }

/* hover fallback if JS disabled */
.service-card:hover {
    transform: translateY(-6px);
}

/* small polish */
.service-card:focus-within {
    outline: 3px solid rgba(123,108,255,0.12);
}
</style>
