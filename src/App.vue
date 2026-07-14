<script setup>
import { onBeforeUnmount, onMounted, reactive, ref } from "vue";
import { injectSpeedInsights } from "@vercel/speed-insights";

const currentYear = new Date().getFullYear();
const isMenuOpen = ref(false);
const activeSection = ref("");
let sectionObserver;

const navLinks = [
  { label: "Program", href: "#program", id: "program" },
  { label: "Membership", href: "#membership", id: "membership" },
  { label: "Coaches", href: "#coaches", id: "coaches" },
  { label: "Location", href: "#location", id: "location" },
  { label: "Contact", href: "#contact", id: "contact" },
];

const trainingLog = [
  {
    set: "01",
    focus: "OPEN FLOOR",
    detail:
      "Full cable-station access. No waiting on a single rack, no signup sheets.",
  },
  {
    set: "02",
    focus: "SMALL GROUPS",
    detail:
      "Eight lifters per session, max. Enough room to actually be spotted.",
  },
  {
    set: "03",
    focus: "FORM AUDIT",
    detail:
      "Every new member gets one session of pure technique work before loading up.",
  },
  {
    set: "04",
    focus: "WALK-IN HOURS",
    detail: "6am to 10pm, seven days. Train around your schedule, not ours.",
  },
];

const programs = [
  {
    name: "Strength Foundations",
    description:
      "A coached entry point for lifters who want cleaner movement, smarter loading, and repeatable gym habits.",
    level: "Beginner to returning lifter",
    duration: "50 minutes",
    benefits: ["Technique audit", "Basic strength plan", "Small group feedback"],
  },
  {
    name: "Personal Coaching",
    description:
      "One-to-one sessions built around your training history, schedule, and specific strength goals.",
    level: "All experience levels",
    duration: "60 minutes",
    benefits: ["Individual programming", "Progress reviews", "Mobility support"],
  },
  {
    name: "Performance Training",
    description:
      "Focused coaching for experienced lifters who want stronger pulls, presses, carries, and athletic output.",
    level: "Intermediate and advanced",
    duration: "75 minutes",
    benefits: ["Higher intensity blocks", "Recovery tracking", "Power development"],
  },
];

const memberships = [
  {
    name: "Starter",
    price: "$120",
    sessions: "4 coached sessions / month",
    features: ["Program access", "Open-floor practice hours", "Monthly form check"],
    recommended: false,
  },
  {
    name: "Forge",
    price: "$205",
    sessions: "8 coached sessions / month",
    features: ["Priority booking", "Personal progress notes", "Open-floor practice hours"],
    recommended: true,
  },
  {
    name: "Unlimited",
    price: "$315",
    sessions: "Flexible coached sessions",
    features: ["Advanced programming", "Weekly coach check-in", "Guest pass each month"],
    recommended: false,
  },
];

const coaches = [
  {
    initials: "LC",
    name: "Lina Chen",
    role: "Head Strength Coach",
    experience: "8 years coaching",
    specialization: "Cable strength, movement quality, novice lifters",
    bio:
      "Lina helps members build reliable technique before chasing heavier numbers. Her sessions are calm, direct, and built around steady progress.",
  },
  {
    initials: "MK",
    name: "Marcus King",
    role: "Performance Coach",
    experience: "10 years coaching",
    specialization: "Athletic strength, conditioning, return-to-training plans",
    bio:
      "Marcus works with active professionals and recreational athletes who need efficient sessions without guesswork or crowded-floor chaos.",
  },
];

// Demo testimonials for portfolio presentation only. Replace with real member quotes before launch.
const testimonials = [
  {
    name: "Nina W.",
    program: "Strength Foundations",
    quote:
      "The coaching is precise without being intimidating. I finally understand what good reps should feel like.",
  },
  {
    name: "David L.",
    program: "Personal Coaching",
    quote:
      "The sessions fit my work week, and the progress notes make it easy to keep momentum between visits.",
  },
  {
    name: "Samir A.",
    program: "Performance Training",
    quote:
      "It feels focused and practical. No wasted time, just clear coaching and enough space to train properly.",
  },
];

const form = reactive({
  name: "",
  email: "",
  phone: "",
  program: "",
  date: "",
  message: "",
});

const errors = reactive({});
const successMessage = ref("");

const preferredPrograms = programs.map((program) => program.name);

function closeMenu() {
  isMenuOpen.value = false;
}

function validateForm() {
  Object.keys(errors).forEach((key) => delete errors[key]);

  if (!form.name.trim()) {
    errors.name = "Full name is required.";
  }

  if (!form.email.trim()) {
    errors.email = "Email is required.";
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errors.email = "Enter a valid email address.";
  }

  if (!form.phone.trim()) {
    errors.phone = "Phone number is required.";
  }

  if (!form.program) {
    errors.program = "Choose a preferred program.";
  }

  if (!form.date) {
    errors.date = "Choose a preferred date.";
  }

  return Object.keys(errors).length === 0;
}

function resetForm() {
  form.name = "";
  form.email = "";
  form.phone = "";
  form.program = "";
  form.date = "";
  form.message = "";
}

function handleSubmit() {
  successMessage.value = "";

  if (!validateForm()) {
    return;
  }

  // Connect Formspree, EmailJS, Firebase, or a custom API here when this demo needs real submissions.
  successMessage.value =
    "Request received. This demo form is ready for a real booking service when you are.";
  resetForm();
}

onMounted(() => {
  // Initialize Vercel Speed Insights
  injectSpeedInsights({ framework: 'vue' });

  const sections = navLinks
    .map((link) => document.getElementById(link.id))
    .filter(Boolean);

  sectionObserver = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          activeSection.value = entry.target.id;
        }
      });
    },
    { rootMargin: "-35% 0px -50% 0px", threshold: 0.1 },
  );

  sections.forEach((section) => sectionObserver.observe(section));
});

onBeforeUnmount(() => {
  sectionObserver?.disconnect();
});
</script>

<template>
  <div>
    <nav class="site-nav" aria-label="Primary navigation">
      <div class="content nav-content">
        <a class="brand" href="#top" @click="closeMenu">ATLAS FORGE</a>

        <button
          class="menu-toggle"
          type="button"
          aria-label="Toggle navigation menu"
          :aria-expanded="isMenuOpen"
          aria-controls="primary-menu"
          @click="isMenuOpen = !isMenuOpen"
        >
          <span></span>
          <span></span>
          <span></span>
        </button>

        <div id="primary-menu" class="nav-menu" :class="{ open: isMenuOpen }">
          <a
            v-for="link in navLinks"
            :key="link.id"
            class="nav-link"
            :class="{ active: activeSection === link.id }"
            :href="link.href"
            @click="closeMenu"
          >
            {{ link.label }}
          </a>
        </div>
      </div>
    </nav>

    <header id="top" class="hero">
      <div class="content hero-content">
        <p class="eyebrow">Cable-machine strength studio &middot; Shenzhen</p>
        <h1>THE WEIGHT DOESN'T CARE HOW YOU FEEL</h1>
        <p class="hero-copy">
          No mirrors. No machines that lift it for you. Open-floor cable stations,
          small coached groups, and a studio built for people who already know why
          they showed up.
        </p>
        <div class="hero-buttons" aria-label="Hero actions">
          <a class="btn-primary" href="#booking">Book a free session</a>
          <a class="btn-ghost" href="#program">See the program</a>
        </div>
      </div>
    </header>

    <main>
      <section class="training-overview" aria-labelledby="training-title">
        <div class="content">
          <p class="eyebrow">// Training log</p>
          <h2 id="training-title">WHAT YOU'RE SIGNING UP FOR</h2>

          <div class="log">
            <div class="log-row reveal" v-for="row in trainingLog" :key="row.set">
              <span>{{ row.set }}</span>
              <span>{{ row.focus }}</span>
              <span>{{ row.detail }}</span>
            </div>
          </div>
        </div>
      </section>

      <section id="program" class="section-anchor" aria-labelledby="program-title">
        <div class="content">
          <div class="section-heading">
            <p class="eyebrow">// Program</p>
            <h2 id="program-title">COACHED WORK THAT HOLDS UP UNDER LOAD</h2>
          </div>

          <div class="card-grid three-column">
            <article class="info-card reveal" v-for="program in programs" :key="program.name">
              <p class="card-kicker">{{ program.level }}</p>
              <h3>{{ program.name }}</h3>
              <p>{{ program.description }}</p>
              <dl class="meta-list">
                <div>
                  <dt>Duration</dt>
                  <dd>{{ program.duration }}</dd>
                </div>
              </dl>
              <ul class="check-list">
                <li v-for="benefit in program.benefits" :key="benefit">{{ benefit }}</li>
              </ul>
              <a class="card-action" href="#booking">Start with this</a>
            </article>
          </div>
        </div>
      </section>

      <section id="membership" class="section-anchor" aria-labelledby="membership-title">
        <div class="content">
          <div class="section-heading">
            <p class="eyebrow">// Membership</p>
            <h2 id="membership-title">SIMPLE PLANS FOR CONSISTENT TRAINING</h2>
          </div>

          <div class="card-grid three-column">
            <article
              class="price-card reveal"
              :class="{ recommended: plan.recommended }"
              v-for="plan in memberships"
              :key="plan.name"
            >
              <p v-if="plan.recommended" class="plan-badge">Recommended</p>
              <h3>{{ plan.name }}</h3>
              <p class="price">{{ plan.price }}<span>/month</span></p>
              <p class="sessions">{{ plan.sessions }}</p>
              <ul class="check-list">
                <li v-for="feature in plan.features" :key="feature">{{ feature }}</li>
              </ul>
              <a class="card-action" href="#booking">Choose {{ plan.name }}</a>
            </article>
          </div>
        </div>
      </section>

      <section id="coaches" class="section-anchor" aria-labelledby="coaches-title">
        <div class="content">
          <div class="section-heading">
            <p class="eyebrow">// Coaches</p>
            <h2 id="coaches-title">CLEAR EYES ON EVERY REP</h2>
          </div>

          <div class="coach-grid">
            <article class="coach-card reveal" v-for="coach in coaches" :key="coach.name">
              <div
                class="coach-photo"
                role="img"
                :aria-label="`${coach.name}, ${coach.role}`"
              >
                {{ coach.initials }}
              </div>
              <div>
                <p class="card-kicker">{{ coach.role }}</p>
                <h3>{{ coach.name }}</h3>
                <p class="coach-meta">{{ coach.experience }} / {{ coach.specialization }}</p>
                <p>{{ coach.bio }}</p>
              </div>
            </article>
          </div>
        </div>
      </section>

      <section class="testimonials" aria-labelledby="testimonials-title">
        <div class="content">
          <div class="section-heading">
            <p class="eyebrow">// Demo member notes</p>
            <h2 id="testimonials-title">WHAT THE FLOOR FEELS LIKE</h2>
          </div>

          <div class="card-grid three-column">
            <figure class="quote-card reveal" v-for="review in testimonials" :key="review.name">
              <blockquote>{{ review.quote }}</blockquote>
              <figcaption>{{ review.name }} / {{ review.program }}</figcaption>
            </figure>
          </div>
        </div>
      </section>

      <section id="location" class="section-anchor" aria-labelledby="location-title">
        <div class="content location-grid">
          <div>
            <p class="eyebrow">// Location</p>
            <h2 id="location-title">A SHENZHEN TRAINING FLOOR BUILT FOR FOCUS</h2>
            <p>
              Atlas Forge is presented as a Shenzhen-based portfolio concept. The
              location details below are structured so a real studio address,
              transport notes, phone, and email can be added quickly.
            </p>
            <div class="location-details">
              <div>
                <h3>Studio Area</h3>
                <p>Shenzhen, China. Exact address shared after booking.</p>
              </div>
              <div>
                <h3>Opening Hours</h3>
                <p>Monday to Sunday, 6:00 AM to 10:00 PM.</p>
              </div>
              <div>
                <h3>Transport</h3>
                <p>Near metro and bus connections. Add station details later.</p>
              </div>
              <div>
                <h3>Contact</h3>
                <p>+86 000 0000 0000<br />hello@atlasforge.example</p>
              </div>
            </div>
          </div>

          <div class="map-placeholder">
            <iframe
              title="OpenStreetMap map centered near Shenzhen"
              src="https://www.openstreetmap.org/export/embed.html?bbox=113.8500%2C22.4300%2C114.2500%2C22.6800&amp;layer=mapnik&amp;marker=22.5431%2C114.0579"
              loading="lazy"
              referrerpolicy="no-referrer"
            ></iframe>
          </div>
        </div>
      </section>

      <section id="contact" class="section-anchor contact-section" aria-labelledby="booking-title">
        <div id="booking" class="content booking-content">
          <div>
            <p class="eyebrow">// First session</p>
            <h2 id="booking-title">BOOK YOUR FREE SESSION</h2>
            <p>
              Tell us where you are starting from. A coach can review the request and
              suggest the best first session once this demo form is connected.
            </p>
          </div>

          <form class="booking-form" novalidate @submit.prevent="handleSubmit">
            <div class="field-group">
              <label for="name">Full name</label>
              <input
                id="name"
                v-model="form.name"
                type="text"
                autocomplete="name"
                :aria-invalid="Boolean(errors.name)"
                :aria-describedby="errors.name ? 'name-error' : undefined"
              />
              <p v-if="errors.name" id="name-error" class="field-error">{{ errors.name }}</p>
            </div>

            <div class="field-group">
              <label for="email">Email</label>
              <input
                id="email"
                v-model="form.email"
                type="email"
                autocomplete="email"
                :aria-invalid="Boolean(errors.email)"
                :aria-describedby="errors.email ? 'email-error' : undefined"
              />
              <p v-if="errors.email" id="email-error" class="field-error">{{ errors.email }}</p>
            </div>

            <div class="field-group">
              <label for="phone">Phone number</label>
              <input
                id="phone"
                v-model="form.phone"
                type="tel"
                autocomplete="tel"
                :aria-invalid="Boolean(errors.phone)"
                :aria-describedby="errors.phone ? 'phone-error' : undefined"
              />
              <p v-if="errors.phone" id="phone-error" class="field-error">{{ errors.phone }}</p>
            </div>

            <div class="field-group">
              <label for="preferred-program">Preferred program</label>
              <select
                id="preferred-program"
                v-model="form.program"
                :aria-invalid="Boolean(errors.program)"
                :aria-describedby="errors.program ? 'program-error' : undefined"
              >
                <option value="">Choose a program</option>
                <option v-for="program in preferredPrograms" :key="program" :value="program">
                  {{ program }}
                </option>
              </select>
              <p v-if="errors.program" id="program-error" class="field-error">
                {{ errors.program }}
              </p>
            </div>

            <div class="field-group">
              <label for="preferred-date">Preferred date</label>
              <input
                id="preferred-date"
                v-model="form.date"
                type="date"
                :aria-invalid="Boolean(errors.date)"
                :aria-describedby="errors.date ? 'date-error' : undefined"
              />
              <p v-if="errors.date" id="date-error" class="field-error">{{ errors.date }}</p>
            </div>

            <div class="field-group full-span">
              <label for="message">Message</label>
              <textarea
                id="message"
                v-model="form.message"
                rows="4"
                placeholder="Training background, goals, or scheduling notes"
              ></textarea>
            </div>

            <button class="submit-button full-span" type="submit">Request Session</button>
            <p v-if="successMessage" class="success-message full-span" role="status">
              {{ successMessage }}
            </p>
          </form>
        </div>
      </section>
    </main>

    <footer class="site-footer">
      <div class="content footer-grid">
        <div>
          <a class="brand" href="#top">ATLAS FORGE</a>
          <p>
            Portfolio demonstration project for a fictional Shenzhen strength-training
            studio.
          </p>
        </div>

        <div>
          <h2>Navigate</h2>
          <a v-for="link in navLinks" :key="link.id" :href="link.href">{{ link.label }}</a>
        </div>

        <div>
          <h2>Contact</h2>
          <p>+86 000 0000 0000</p>
          <p>hello@atlasforge.example</p>
        </div>

        <div>
          <h2>Social</h2>
          <span class="footer-placeholder">Instagram to be added</span>
          <span class="footer-placeholder">WeChat to be added</span>
          <span class="footer-placeholder">LinkedIn to be added</span>
        </div>
      </div>
      <p class="footer-bottom">
        &copy; {{ currentYear }} Atlas Forge. Fictional portfolio demonstration project made by Zakaria.
      </p>
    </footer>
  </div>
</template>
