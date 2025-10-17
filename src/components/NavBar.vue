<template>
    <nav class="navbar" :class="{ 'nav-hidden': isHidden }">
        <div class="navbar-logo">
            <!-- replace $SELECTION_PLACEHOLDER$ with this -->
            <a href="#hero" @click.prevent="scrollTo('hero')">
                <slot name="logo">
                    <span>Sadowski</span>
                </slot>
            </a>
        </div>
        <ul class="navbar-links">
            <!-- Usługi -> scroll to #services -->
            <li><a href="#services" @click.prevent="scrollTo('services')">Usługi</a></li>

            <!-- O nas does not exist yet, skipped -->

            <!-- Kontakt -> scroll to #contact -->
            <li><a href="#contact" @click.prevent="scrollTo('contact')">Kontakt</a></li>
        </ul>
    </nav>
</template>

<script>
export default {
    name: 'NavBar',
    data() {
        return {
            isHidden: false,
            lastScrollY: 0
        }
    },
    mounted() {
        this.lastScrollY = window.scrollY || window.pageYOffset;
        window.addEventListener('scroll', this.onScroll, { passive: true });
    },
    beforeDestroy() { // Vue 2
        window.removeEventListener('scroll', this.onScroll);
    },
    unmounted() { // Vue 3
        window.removeEventListener('scroll', this.onScroll);
    },
    methods: {
        onScroll() {
            const current = window.scrollY || window.pageYOffset;
            // hide when scrolling down, show when scrolling up; require some offset so it doesn't hide at top
            this.isHidden = current > this.lastScrollY && current > 50;
            this.lastScrollY = current <= 0 ? 0 : current;
        },
        scrollTo(id) {
            const el = document.getElementById(id);
            if (el) {
                el.scrollIntoView({ behavior: 'smooth', block: 'start' });
                if(id !== 'hero')
                    setTimeout(() => {
                        this.isHidden = true;
                    }, 200) // np. 500ms, dopasuj do długości scrolla
            } else {
                // optionally warn in dev if target doesn't exist
                if (process.env.NODE_ENV !== 'production') {
                    // eslint-disable-next-line no-console
                    console.warn(`Scroll target #${id} not found`);
                }
            }
        }
    }
}
</script>

<style scoped>
.navbar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background: rgba(34, 34, 34, 0.5); /* semi-transparent background */
    color: #fff;
    padding: 0.5rem 2rem;
    z-index: 1000;
    transition: transform 0.25s ease, opacity 0.25s ease;
    backdrop-filter: blur(4px); /* optional: nicer look behind translucent navbar */
}

/* hidden state: slide up out of view */
.nav-hidden {
    transform: translateY(-100%);
    pointer-events: none;
}

.navbar-logo span {
    font-weight: bold;
    font-size: 1.5rem;
}

.navbar-links {
    list-style: none;
    display: flex;
    gap: 1.5rem;
    margin: 0;
    padding: 0;
}

a {
    color: #fff;
    text-decoration: none;
    transition: color 0.2s;
    cursor: pointer;
}

.navbar-links a:hover {
    color: #00bcd4;
}
</style>