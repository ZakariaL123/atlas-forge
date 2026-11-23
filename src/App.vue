<script setup>
import { nextTick, onBeforeUnmount, onMounted, reactive, ref } from "vue";
import heroPhoto from "./assets/hero-bg.jpg";

const currentYear = new Date().getFullYear();
const isMenuOpen = ref(false);
const menuToggle = ref(null);
const isMapOpen = ref(false);
const selectedPlan = ref("");
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

function dismissMenu(event) {
  if (event.key !== "Escape" || !isMenuOpen.value) return;

  closeMenu();
  menuToggle.value?.focus();
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

async function handleSubmit(event) {
  successMessage.value = "";

  if (!validateForm()) {
    const bookingForm = event.currentTarget;
    await nextTick();
    bookingForm.querySelector('[aria-invalid="true"]')?.focus();
    return;
  }

  successMessage.value = "Demo complete. Your sample booking passed validation.";
  resetForm();
  selectedPlan.value = "";
}

onMounted(() => {
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
    { rootMargin: "-35% 0px -50% 0px", threshold: 0 },
  );

  sections.forEach((section) => sectionObserver.observe(section));
});

onBeforeUnmount(() => {
  sectionObserver?.disconnect();
});
</script>

<template>
  <div>
    <a class="skip-link" href="#main-content">Skip to content</a>
    <nav
      class="site-nav"
      aria-label="Primary navigation"
      @keydown="dismissMenu"
      @focusout="!$event.currentTarget.contains($event.relatedTarget) && closeMenu()"
    >
      <div class="content nav-content">
        <a class="brand" href="#top" @click="closeMenu">ATLAS FORGE</a>

        <button
          ref="menuToggle"
          class="menu-toggle"
          type="button"
          :aria-expanded="isMenuOpen"
          aria-controls="primary-menu"
          @click="isMenuOpen = !isMenuOpen"
        >
          {{ isMenuOpen ? "Close" : "Menu" }}
        </button>

        <div id="primary-menu" class="nav-menu" :class="{ open: isMenuOpen }">
          <a
            v-for="link in navLinks"
            :key="link.id"
            class="nav-link"
            :aria-current="activeSection === link.id ? 'location' : undefined"
            :href="link.href"
            @click="closeMenu"
          >
            {{ link.label }}
          </a>
        </div>
      </div>
    </nav>

    <main id="main-content" tabindex="-1">
      <header id="top" class="hero">
        <div class="content hero-content">
          <div class="hero-intro">
            <h1>
              <span>The weight</span>
              <span>doesn’t care</span>
              <span>how you feel</span>
            </h1>
            <p class="hero-copy">Open-floor cable stations and small-group strength coaching.</p>
            <div class="hero-buttons">
              <a class="btn-primary" href="#booking">Try the booking demo</a>
              <a class="btn-ghost" href="#program">See the programs</a>
            </div>
            <p class="concept-note">A fictional strength studio in Shenzhen</p>
          </div>
          <img
            class="hero-photo"
            :src="heroPhoto"
            alt="Black weight plates mounted on a steel strength machine"
            width="2400"
            height="3600"
            fetchpriority="high"
          />
        </div>
      </header>

      <section class="training-overview" aria-labelledby="training-title">
        <div class="content">
          <h2 id="training-title">On the training floor</h2>

          <div class="log">
            <div class="log-row" v-for="row in trainingLog" :key="row.set">
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
            <h2 id="program-title">Training programs</h2>
          </div>

          <div class="card-grid three-column">
            <article class="info-card" v-for="program in programs" :key="program.name">
              <h3>{{ program.name }}</h3>
              <p class="card-kicker">{{ program.level }}</p>
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
              <a class="card-action" href="#booking" @click="form.program = program.name">
                Try {{ program.name }}
              </a>
            </article>
          </div>
        </div>
      </section>

      <section id="membership" class="section-anchor" aria-labelledby="membership-title">
        <div class="content">
          <div class="section-heading">
            <h2 id="membership-title">Membership plans</h2>
          </div>

          <div class="card-grid three-column">
            <article
              class="price-card"
              :class="{ recommended: plan.recommended }"
              v-for="plan in memberships"
              :key="plan.name"
            >
              <h3>{{ plan.name }}</h3>
              <p v-if="plan.recommended" class="plan-badge">Recommended</p>
              <p class="price">{{ plan.price }}<span>/month</span></p>
              <p class="sessions">{{ plan.sessions }}</p>
              <ul class="check-list">
                <li v-for="feature in plan.features" :key="feature">{{ feature }}</li>
              </ul>
              <a class="card-action" href="#booking" @click="selectedPlan = plan.name">
                Choose {{ plan.name }}
              </a>
            </article>
          </div>
        </div>
      </section>

      <section id="coaches" class="section-anchor" aria-labelledby="coaches-title">
        <div class="content">
          <div class="section-heading">
            <h2 id="coaches-title">Meet the coaches</h2>
          </div>

          <div class="coach-grid">
            <article class="coach-card" v-for="coach in coaches" :key="coach.name">
              <div
                class="coach-photo"
                aria-hidden="true"
              >
                {{ coach.initials }}
              </div>
              <div>
                <h3>{{ coach.name }}</h3>
                <p class="card-kicker">{{ coach.role }}</p>
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
            <h2 id="testimonials-title">Fictional member stories</h2>
          </div>

          <div class="card-grid three-column">
            <figure class="quote-card" v-for="review in testimonials" :key="review.name">
              <blockquote>{{ review.quote }}</blockquote>
              <figcaption>{{ review.name }} / {{ review.program }}</figcaption>
            </figure>
          </div>
        </div>
      </section>

      <section id="location" class="section-anchor" aria-labelledby="location-title">
        <div class="content location-grid">
          <div>
            <h2 id="location-title">The Shenzhen concept</h2>
            <p>Shenzhen is the setting for this portfolio concept. Atlas Forge has no physical address.</p>
            <dl class="location-details">
              <div>
                <dt>Setting</dt>
                <dd>Shenzhen, China</dd>
              </div>
              <div>
                <dt>Sample opening hours</dt>
                <dd>Daily, 6:00 AM to 10:00 PM</dd>
              </div>
            </dl>
          </div>

          <div class="map-panel">
            <h3>Explore the area</h3>
            <p id="map-note">The map shows Shenzhen without a studio pin. Opening it connects to OpenStreetMap.</p>
            <button
              class="btn-ghost"
              type="button"
              :aria-expanded="isMapOpen"
              aria-controls="studio-area-map"
              aria-describedby="map-note"
              @click="isMapOpen = !isMapOpen"
            >
              {{ isMapOpen ? "Hide area map" : "Show area map" }}
            </button>
            <div id="studio-area-map" :hidden="!isMapOpen" class="map-frame">
              <iframe
                v-if="isMapOpen"
                title="OpenStreetMap area overview of Shenzhen"
                src="https://www.openstreetmap.org/export/embed.html?bbox=113.8500%2C22.4300%2C114.2500%2C22.6800&amp;layer=mapnik"
                sandbox="allow-scripts allow-same-origin"
                referrerpolicy="no-referrer"
              ></iframe>
            </div>
          </div>
        </div>
      </section>

      <section id="contact" class="section-anchor contact-section" aria-labelledby="booking-title">
        <div id="booking" class="content booking-content" tabindex="-1">
          <div>
            <h2 id="booking-title">Try booking a session</h2>
            <p>Use sample details to try the form. Your details stay on this page and no session is reserved.</p>
            <p v-if="selectedPlan" class="chosen-plan">Selected demo plan: {{ selectedPlan }}</p>
          </div>

          <form class="booking-form" autocomplete="off" novalidate @submit.prevent="handleSubmit">
            <div class="field-group">
              <label for="name">Full name</label>
              <input
                id="name"
                v-model="form.name"
                type="text"
                required
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
                required
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
                required
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
                required
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
                required
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

            <button class="submit-button full-span" type="submit">Validate demo booking</button>
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
          <h2>Project</h2>
          <a href="https://github.com/ZakariaL123/atlas-forge">View source code</a>
        </div>
      </div>
      <p class="footer-bottom">
        &copy; {{ currentYear }} Atlas Forge. Fictional portfolio demonstration project made by Zakaria.
      </p>
    </footer>
  </div>
</template>
