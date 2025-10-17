<template>
    <section id="contact" class="contact" ref="contact">
        <h2 class="animate">Kontakt</h2>

        <div class="contact-grid">
            <div class="contact-card contact-details" ref="details">
                <ul>
                    <li class="contact-item animate">
                        <strong>Email:</strong>
                        <a href="mailto:example@email.com">example@email.com</a>
                    </li>
                    <li class="contact-item animate">
                        <strong>Telefon:</strong>
                        <a href="tel:+48607500340">607 500 340</a>
                    </li>
                    <li class="contact-item animate">
                        <strong>Adres:</strong>
                        <span>Gryfice, Osadnicza 2, 72-300</span>
                    </li>
                </ul>
            </div>

            <div class="contact-card contact-map animate">
                <div class="map-wrap">
                    <iframe
                        src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2349.4359432635583!2d15.218530212807046!3d53.923998531229934!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x4700649382c7f1a7%3A0x459fabd88a2a503e!2sOsadnicza%202%2C%2072-300%20Gryfice!5e0!3m2!1spl!2spl!4v1760523131837!5m2!1spl!2spl"
                        width="100%"
                        height="100%"
                        style="border:0;"
                        allowfullscreen=""
                        loading="lazy"
                        referrerpolicy="no-referrer-when-downgrade"
                    ></iframe>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

export default {
    name: "Contact",
    setup() {
        const contact = ref(null);
        const details = ref(null);
        let createdTriggers = [];

        onMounted(() => {
            gsap.registerPlugin(ScrollTrigger);

            // animate header + items + map with stagger
            const container = contact.value;
            if (!container) return;

            const header = container.querySelector("h2");
            const items = container.querySelectorAll(".contact-item");
            const mapCard = container.querySelector(".contact-map");

            // header animation
            createdTriggers.push(
                gsap.from(header, {
                    y: -20,
                    opacity: 0,
                    duration: 0.7,
                    ease: "power3.out",
                    scrollTrigger: {
                        trigger: header,
                        start: "top 90%",
                        toggleActions: "play none none reverse"
                    }
                })
            );

            // list stagger
            createdTriggers.push(
                gsap.from(items, {
                    y: 20,
                    opacity: 0,
                    duration: 0.7,
                    stagger: 0.12,
                    ease: "power3.out",
                    scrollTrigger: {
                        trigger: details.value,
                        start: "top 85%",
                        toggleActions: "play none none reverse"
                    }
                })
            );

            // map fade/scale in
            createdTriggers.push(
                gsap.from(mapCard, {
                    y: 30,
                    opacity: 0,
                    scale: 0.98,
                    duration: 0.9,
                    ease: "power3.out",
                    scrollTrigger: {
                        trigger: mapCard,
                        start: "top 85%",
                        toggleActions: "play none none reverse"
                    }
                })
            );
        });

        onBeforeUnmount(() => {
            // cleanup ScrollTrigger instances and tweens created by this component
            ScrollTrigger.getAll().forEach((t) => t.kill());
            createdTriggers.forEach((t) => {
                try {
                    t.kill && t.kill();
                } catch {}
            });
            gsap.killTweensOf("*");
        });

        return { contact, details };
    }
};
</script>

<style scoped>
:root {
    --card-bg: #ffffff;
    --muted: #6b7280;
    --accent: #0ea5e9;
    --surface: #f8fafc;
    --glass: rgba(255, 255, 255, 0.6);
}

/* container */
.contact {
    max-width: 1100px;
    margin: 3rem auto;
    padding: 1.25rem;
    box-sizing: border-box;
    color: #0f172a;
}
.contact h2 {
    font-size: 1.75rem;
    margin: 0 0 1.25rem;
    letter-spacing: -0.02em;
    padding-bottom: 1.25rem;
    text-align: center;
}

/* grid layout */
.contact-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.25rem;
    align-items: start;
}

/* card style shared */
.contact-card {
    background: linear-gradient(180deg, var(--card-bg), var(--surface));
    border-radius: 12px;
    padding: 1rem;
    box-shadow: 0 6px 18px rgba(15, 23, 42, 0.08);
    border: 1px solid rgba(15, 23, 42, 0.04);
}

/* details */
.contact-details ul {
    list-style: none;
    padding: 0;
    margin: 0;
}
.contact-item {
    display: flex;
    gap: 0.5rem;
    align-items: baseline;
    padding: 0.65rem 0;
    border-bottom: 1px dashed rgba(15, 23, 42, 0.04);
}
.contact-item:last-child {
    border-bottom: none;
}
.contact-item strong {
    width: 5.5rem;
    color: var(--muted);
    font-weight: 600;
    font-size: 0.95rem;
}
.contact-item a,
.contact-item span {
    color: #0f172a;
    font-size: 0.98rem;
    text-decoration: none;
    word-break: break-word;
}
.contact-item a {
    color: var(--accent);
}
.contact-item a:hover {
    text-decoration: underline;
}

/* map */
.contact-map .map-wrap {
    height: 280px;
    border-radius: 8px;
    overflow: hidden;
    border: 1px solid rgba(15, 23, 42, 0.06);
    box-shadow: inset 0 -10px 30px rgba(10, 15, 25, 0.02);
}

/* responsive */
@media (max-width: 800px) {
    .contact-grid {
        grid-template-columns: 1fr;
    }
    .contact-map .map-wrap {
        height: 320px;
    }
}

/* subtle focus for links when keyboard navigating */
.contact a:focus {
    outline: 3px solid rgba(14, 165, 233, 0.18);
    outline-offset: 3px;
    border-radius: 4px;
}
</style>