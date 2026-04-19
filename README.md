# ChefGPT - AI-Powered Recipe Generator

ChefGPT is a web application that uses AI to generate personalized recipes from ingredient photos. Users can snap a photo of their ingredients, set their food preferences, and receive custom recipes tailored to their dietary needs.

## Tech Stack

- **Backend:** Java 17, Spring Boot 3.5.9
- **Frontend:** Thymeleaf, Bootstrap 5.3.2, Bootstrap Icons
- **Database:** MySQL 8.x
- **ORM:** Spring Data JPA / Hibernate
- **Auth:** Spring Security (BCrypt, session-based)
- **AI:** Claude API (Anthropic) for image recognition and recipe generation
- **Payments:** Stripe
- **Email:** Spring Mail + Gmail SMTP (App Password)
- **Build:** Maven
- **Testing:** JUnit 5, Mockito, H2 in-memory database

## Features

- **User System** — Register, login, profile, avatar upload, password reset via email (Gmail SMTP)
- **Food Preferences** — 4-step onboarding (diet, allergies, cuisines, spice level)
- **Image Recognition** — Upload ingredient photos, AI detects ingredients with confidence scores
- **Recipe Generation** — AI generates recipes based on ingredients and preferences
- **Recipe Management** — Save, share, rate, and view recipes
- **Meal Planner** — Schedule recipes by date and meal type
- **Leaderboard** — Top recipes and users ranked by ratings
- **Subscription** — Free/Beginner/Premium/Pro tiers with Stripe payments
- **Admin Dashboard** — Manage users (enable/disable) and recipes (view/delete)

## Prerequisites

- Java 17+
- Maven (or use included `mvnw` wrapper)
- MySQL 8.x
- Anthropic API key (Claude)
- Stripe API keys (test mode)
- Gmail account with App Password (for password reset emails)

## Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/Heng2510/recipe-generation.git
   cd recipe-generation
   ```

2. **Create MySQL database**
   ```sql
   CREATE DATABASE recipes_db;
   ```

3. **Set environment variables**

   Set these in your IDE run configuration (IntelliJ: Edit Configurations → Environment Variables):
   ```
   # Database
   MYSQL_DATABASE=recipes_db
   MYSQL_USER=root
   MYSQL_PASSWORD=your_password

   # Claude AI
   ANTHROPIC_API_KEY=your_key
   ANTHROPIC_API_URL=https://api.anthropic.com/v1/messages
   ANTHROPIC_API_MODEL=claude-3-haiku-20240307
   ANTHROPIC_API_VERSION=2023-06-01
   ANTHROPIC_API_MAX_TOKEN=2000

   # Stripe (test mode)
   STRIPE_SECRET_KEY=sk_test_...
   STRIPE_PUBLISH_KEY=pk_test_...
   PRICE_BEGINNER=price_...
   PRICE_PREMIUM=price_...
   PRICE_PRO=price_...

   # Email (Gmail SMTP)
   MAIL_HOST=smtp.gmail.com
   MAIL_PORT=587
   MAIL_USERNAME=your_email@gmail.com
   MAIL_PASSWORD=your_16_char_app_password
   ```

   > **Gmail setup:** Enable 2-Step Verification on your Google account, then generate an App Password at https://myaccount.google.com/apppasswords. Use the 16-character App Password (no spaces) as `MAIL_PASSWORD`.

4. **Run the application**
   ```bash
   ./mvnw spring-boot:run
   ```

5. **Open in browser**
   ```
   http://localhost:8080
   ```

## Admin Setup

The application supports role-based access control with `USER` and `ADMIN` roles. To create an admin user:

1. Register a normal account through the app.
2. Run this SQL query in MySQL:
   ```sql
   UPDATE users SET role = 'ADMIN' WHERE email = 'your_admin@email.com';
   ```
3. Log out and log back in — admin features are now accessible at `/admin`.

Admin pages (`/admin/**`) are restricted to `ADMIN` role via Spring Security.

## Testing

35 automated tests across 3 testing methods: Unit, Integration, and Hybrid.

**Run all tests:**
```bash
export JAVA_HOME="$HOME/.jdks/openjdk-25.0.2"
./mvnw test
```

**Run from IDE:** Click the ▶ icon next to any test method or class in IntelliJ.

**Test structure:**
```
src/test/java/io/github/hungle/recipe_api/
├── RecipeApiApplicationTests.java              # Spring context smoke test (1 test)
├── service/
│   ├── UserServiceTest.java                    # Unit tests — UserService (10 tests)
│   └── RecipeServiceTest.java                  # Unit tests — RecipeService (11 tests)
├── integration/
│   └── UserRegistrationIntegrationTest.java    # Integration tests — full stack + H2 (8 tests)
└── hybrid/
    └── AdminViewControllerHybridTest.java      # Hybrid tests — web layer + mocked services (5 tests)

src/test/resources/
└── application-test.yaml                       # H2 in-memory DB config (no MySQL needed)
```

**Testing methods used:**

| Method | Framework | What it tests |
|--------|-----------|---------------|
| Unit | JUnit 5 + Mockito (`@Mock`, `@InjectMocks`) | Service logic in isolation, no DB |
| Integration | `@SpringBootTest` + `@AutoConfigureMockMvc` + H2 | Full HTTP → Controller → Service → DB flow |
| Hybrid | `@WebMvcTest` + `@MockitoBean` + MockMvc | Web layer routing with mocked services |

## Project Structure

```
src/main/java/io/github/hungle/recipe_api/
├── config/          # Security, WebMvc, login handler, global model
├── controller/
│   ├── api/         # REST endpoints (AdminController, UserRestController)
│   └── web/         # Thymeleaf controllers (8 controllers: user, recipe,
│                    #   admin, home, homepage, leaderboard, meal plan, subscription)
├── dto/             # Data transfer objects
├── entity/          # JPA entities (10: User, Recipe, Subscription, etc.)
├── enums/           # Role, DietType, AllergyType, CuisineType, etc.
├── exception/       # Custom exceptions + global handler
├── repository/      # Spring Data JPA repositories (7 interfaces)
└── service/         # Business logic (10 service classes)

src/main/resources/
├── application.yaml
├── static/css/      # 16 stylesheets
├── static/images/   # Logo and assets
└── templates/       # 28 Thymeleaf HTML pages
    ├── admin/       # Dashboard, user management, recipe management
    ├── common/      # Header and footer fragments
    ├── meal-plan/   # Create, list, view
    ├── recipe/      # Import, correction, result, my-recipes
    ├── subscription/# Subscription plans
    └── user/        # Login, register, profile, preferences, etc.
```

## Team

| Member | Role |
|--------|------|
| Chieu Vinh Hung Le | CEO / Lead Developer |
| Nguyen Mai Dinh Le | Developer |
| Mahamadreza | Developer |
| Nguyen Le Tuan | Developer |

## License

This project was developed as part of CSIS 3280 - Software Engineering at Douglas College.
