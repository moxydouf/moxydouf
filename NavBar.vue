<template>
  <!-- Navigation bar with brand logo and links for navigation -->
  <nav class="navbar-expand-lg bg-body-tertiary nav-bar">
    <div class="container-fluid">
      <!-- Link to home page with logo -->
      <router-link
        class="navbar-brand"
        @click="leaveLobby()"
        :to="{ name: 'home' }"
      >
        <img :src="logo" alt="KI Master Logo" class="logo" />
      </router-link>
      <div class="navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <!-- Conditional link for instructions page, shown only on starting page -->
          <li class="nav-item" v-if="isStartingPage">
            <router-link class="nav-link" :to="{ name: 'instruction' }">{{
              $t("message.instruction")
            }}</router-link>
          </li>
        </ul>
      </div>
      <!-- Controls for theme switcher and language selection -->
      <div class="top-right-controls">
        <label class="switch">
          <input
            type="checkbox"
            v-model="isDarkMode"
            @change="toggleDarkMode"
          />
          <span class="slider round"></span>
        </label>
        <language-switcher class="me-2"></language-switcher>
      </div>
    </div>
    <!-- Rules Dialog Component -->
    <teleport to="body">
      <base-dialog
        :title="$t('rules.game_title')"
        v-if="isRulesVisible"
        @close="closeRules"
      >
        <component :is="currentRuleComponent" />
        <template #actions>
          <base-button @click="closeRules">{{
            $t("message.okay")
          }}</base-button>
        </template>
      </base-dialog>
    </teleport>
  </nav>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import { useRoute } from "vue-router";
// Importing rule components for various games
import Connect4Rules from "@/components/gameRules/Connect4Rules.vue";
import NimRules from "@/components/gameRules/NimRules.vue";
import OthelloRules from "@/components/gameRules/OthelloRules.vue";
import TicTacToeRules from "@/components/gameRules/TicTacToeRules.vue";
import PlayPageLogic from "../UI/PlayPage.js";
import BaseDialog from "@/components/UI/BaseDialog.vue";
import LanguageSwitcher from "./LanguageSwitcher.vue";
import logo from "@/components/icons/logo.png"; // Import the logo image

/**
 * NavBar component that includes a Home Button and language selection Options
 * @module NavBar
 */

export default {
  components: {
    Connect4Rules,
    NimRules,
    OthelloRules,
    TicTacToeRules,
    LanguageSwitcher,
    BaseDialog,
  },
  mixins: [PlayPageLogic],
  data() {
    return {
      /**
       * Current language of the application */
      currentLanguage: this.$i18n.locale,
      /** Whether the rules dialog is visible */
      isRulesVisible: false,
      /** - Component name to be displayed in the rules dialog */
      currentRuleComponent: null,
      /** - Path to the logo image */
      logo, // Add logo path to data function
    };
  },
  computed: {
    /**
     * Checks if the current route is the starting page.
     * @returns {boolean} - True if on the starting page, otherwise false.
     * @method
     */
    isStartingPage() {
      return this.$route.name === "home";
    },
    /**
     * Checks if the current route is the play page.
     * @returns {boolean} - True if on the play page, otherwise false.
     */
    isPlayPage() {
      return this.$route.name === "play";
    },
    /**
     * Checks if the current route is the lobby page.
     * @returns {boolean} - True if on the lobby page, otherwise false.
     */
    isLobbyPage() {
      return this.$route.name === "lobby";
    },
    /**
     * Vuex getter for game active state.
     * @type {boolean}
     */
    ...mapGetters(["gameActive"]),
    ...mapGetters(["isDarkMode"]),
  },
  methods: {
    /**
     * Maps Vuex actions to the component.
     * @type {Function}
     */
    ...mapActions(["sendWebSocketMessage"]),

    /**
     * Sends a WebSocket message.
     * @param {Object} data - Data to be sent in the message.
     */
    sendMessage(data) {
      console.log(data);
      this.sendWebSocketMessage(JSON.stringify(data));
    },

    /**
     * Handles leaving the lobby based on the current route and game state.
     */
    leaveLobby() {
      if (
        this.$route.name === "lobby" ||
        this.$route.name === "wait" ||
        (this.$route.name === "play" && !this.gameActive) ||
        (this.$route.name === "instructions" && !this.gameActive) ||
        (this.$route.name === "impressum" && !this.gameActive) ||
        (this.$route.name === "about" && !this.gameActive)
      ) {
        const data = {
          command: "lobby",
          command_key: "leave",
        };
        this.sendMessage(data);
      }
    },

    /**
     * Toggles the application language.
     */
    changeLanguage() {
      if (this.$i18n.locale === "en") {
        this.$i18n.locale = "de";
        this.currentLanguage = "de";
      } else {
        this.$i18n.locale = "en";
        this.currentLanguage = "en";
      }
      this.$nextTick(() => {
        document.querySelector(".form-select").blur();
      });
    },

    /**
     * Shows the rules dialog based on the current game.
     */
    showRules() {
      if (this.game === "connect4") {
        this.currentRuleComponent = "Connect4Rules";
      } else if (this.game === "tictactoe") {
        this.currentRuleComponent = "TicTacToeRules";
      } else if (this.game === "nim") {
        this.currentRuleComponent = "NimRules";
      } else if (this.game === "othello") {
        this.currentRuleComponent = "OthelloRules";
      } else {
        this.currentRuleComponent = null;
      }
      this.isRulesVisible = true;
    },
    ...mapActions(["toggleDarkMode"]),
    /**
     * Closes the rules dialog.
     */
    closeRules() {
      this.isRulesVisible = false;
    },
  },
};
</script>

<style scoped>
/* Styles for the navigation bar container */
.navbar-expand-lg {
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
}

.container-fluid {
  padding-left: 15px;
  padding-right: 15px;
  margin-right: auto;
  margin-left: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
  position: relative;
}

/* Styles for the navbar brand (logo and text) */
.navbar-brand {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
  display: flex;
  align-items: center; /* Ensures the logo is vertically centered */
}

/* Styles for navigation items */
.nav-item {
  margin-left: 10px;
}


/* Hover effect for navigation links */
.nav-link:hover {
  color: #007bff;
}

/* Styles for the collapsible navbar */
.navbar-collapse {
  display: flex;
  flex-grow: 1;
  justify-content: space-between;
  align-items: center;
}

/* Styles for the top-right controls (toggle switch and language switcher) */
.top-right-controls {
  display: flex;
  align-items: center;
  position: absolute;
  right: 10px;
}

/* Toggle Switch Styles */
.switch {
  position: relative;
  display: inline-block;
  width: 60px;
  height: 34px;
  margin-right: 10px;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  transition: 0.4s;
  border-radius: 34px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: #2196f3;
}

input:checked + .slider:before {
  transform: translateX(26px);
}

/* Styles for the logo image */
.logo {
  width: auto;
  height: 30px; /* Adjust the height to fit within the navbar */
  max-height: 40px; /* Ensure it doesn't overflow the navbar */
  aspect-ratio: 2.31; /* Maintain the aspect ratio */
}

.dark-mode .navbar-brand {
  color: #fff;
}

.dark-mode .navbar-brand:hover {
  color: #ddd;
}

.dark-mode .nav-link {
  color: #ccc;
}

.dark-mode .nav-link:hover {
  color: #00bfff;
}

.dark-mode .navbar-collapse {
  background-color: #1e1e1e;
}

.dark-mode .top-right-controls {
  color: #ccc;
}

.dark-mode .switch .slider {
  background-color: #444;
}

.dark-mode .switch input:checked + .slider {
  background-color: #00bfff;
}

.dark-mode .bg-body-tertiary {
  background-color: #1e1e1e !important;
}

.dark-mode .logo {
  filter: brightness(0.8);
}












/* Responsive adjustments for mobile view */
@media (max-width: 1100px) and (max-height: 1400px)  {
  .dark-mode .navbar-collapse {
    background-color: #1e1e1e;
  }
  .dark-mode.nav-link{
    background-color: #1e1e1e;
    color: #9dcc67;
  }
  .nav-link {
    font-family: 'Honk', sans-serif;
    font-size: 25px;
    font-weight: bolder;
    color: #98ca60;
    font-size: small;
  }
  .me-2 {
    margin-right: 0px !important;
    padding: 0px;
    font-family: 'Honk', sans-serif;

  }
}
</style>
