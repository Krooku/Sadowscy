<template>
    <section id="services" class="services">
        <h2>Nasze Usługi</h2>
        <div class="service-list">
            <div class="service-card">
                <h3>Wymiana szyby czołowej</h3>
                <p>
                    Oferujemy profesjonalną wymianę szyby czołowej dla wszystkich marek i modeli. Nasi doświadczeni technicy używają wysokiej jakości szkła i materiałów, aby zapewnić Twoje bezpieczeństwo i satysfakcję.
                </p>
            </div>
            <div class="service-card">
                <h3>Naprawa szyby czołowej</h3>
                <p>
                    Masz odprysk lub pęknięcie? Nasza usługa naprawy szybko i skutecznie usuwa drobne uszkodzenia, pomagając uniknąć pełnej wymiany i oszczędzając Twój czas oraz pieniądze.
                </p>
            </div>
        </div>
    </section>
</template>

<script>
import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

export default {
    name: "Services",
    mounted() {
        // timeline that triggers when component scrolls into view
        const tl = gsap.timeline({
            scrollTrigger: {
                trigger: this.$el,
                start: "top 70%",    // when top of section reaches 80% of viewport
                toggleActions: "play none none none",
                // markers: true // uncomment for debugging
            }
        });

        tl.from(this.$el.querySelector("h2"), {
            y: 30,
            opacity: 0,
            duration: 0.6,
            ease: "power3.out"
        })
        .from(this.$el.querySelectorAll(".service-card"), {
            y: 20,
            opacity: 0,
            stagger: 0.15,
            duration: 0.6,
            ease: "power3.out"
        }, "-=0.35");

        // hover animations for cards
        const cards = this.$el.querySelectorAll(".service-card");
        cards.forEach(card => {
            const enter = () => gsap.to(card, { scale: 1.03, boxShadow: "0 8px 20px rgba(0,0,0,0.12)", duration: 0.25, ease: "power1.out" });
            const leave = () => gsap.to(card, { scale: 1, boxShadow: "0 2px 8px rgba(0,0,0,0.07)", duration: 0.25, ease: "power1.out" });
            card.addEventListener("mouseenter", enter);
            card.addEventListener("mouseleave", leave);

            // cleanup on unmount (in case component is destroyed)
            card._gsapCleanup = () => {
                card.removeEventListener("mouseenter", enter);
                card.removeEventListener("mouseleave", leave);
            };
        });

        // store cleanup references
        this._serviceCards = cards;
        this._tl = tl;
    },
    beforeUnmount() {
        // remove hover listeners
        if (this._serviceCards) {
            this._serviceCards.forEach(card => {
                if (card._gsapCleanup) card._gsapCleanup();
            });
        }

        // kill timeline and its ScrollTrigger
        if (this._tl) {
            try {
                if (this._tl.scrollTrigger) this._tl.scrollTrigger.kill();
            } catch (e) {}
            try { this._tl.kill(); } catch (e) {}
            this._tl = null;
        }
    }
};
</script>

<style scoped>
.services {
    padding: 2rem;
    background: #f7f7f7;
    color: var(--text-color);
}
h2 {
    text-align: center;
    margin-bottom: 1.5rem;
}
.service-list {
    display: flex;
    gap: 2rem;
    justify-content: center;
    flex-wrap: wrap;
}
.service-card {
    background: #fff;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.07);
    padding: 1.5rem;
    max-width: 350px;
    flex: 1 1 300px;
    transform-origin: center;
    transition: transform 0.2s;
    cursor: default;
}
.service-card:hover {
    /* fallback if JS disabled */
    transform: translateY(-4px);
}
h3 {
    margin-top: 0;
}
</style>