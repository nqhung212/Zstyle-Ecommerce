# ZStyle - E-commerce with Testing Focus

An e-commerce platform for fashion products built with PHP and MySQL, with emphasis on comprehensive test coverage including 65 automated tests (unit, API, UI, and end-to-end).

## Contents

- [About](#about)
- [Setup](#setup)
- [Running Tests](#running-tests)
- [Test Suite](#test-suite)
- [Application Interface](#application-interface)
- [Docker](#docker)
- [CI/CD](#cicd)
- [Project Structure](#project-structure)
- [Development](#development)
- [Troubleshooting](#troubleshooting)

## About

ZStyle is an e-commerce platform for fashion products (t-shirts, jackets, polos, etc.). The project demonstrates comprehensive testing practices including:

- 23 unit tests for business logic
- 20 API tests for endpoints
- 15 functional UI tests
- 1 end-to-end test (complete shopping flow)
- 5 smoke tests for deployment verification

The test suite covers authentication, product catalog, shopping cart, checkout, admin operations, and security validation.

### Technology Stack

**Backend**: PHP 8.1+, MySQL 8.0+, MVC Architecture  
**Testing**: PHPUnit 10.5.58, Selenium WebDriver, Guzzle  
**Infrastructure**: Docker, Docker Compose, GitHub Actions

## Setup

### Using Docker (Recommended)

```bash
# Clone and navigate to project
git clone https://github.com/Plinh-Ctuyen-QHung-VHau/Zstyle.git
cd Zstyle

# Start application
docker compose up -d

# Start Selenium for UI tests (optional)
docker compose -f docker-compose.selenium.yml up -d

# Verify containers
docker ps
```

Access the application at:

- Main app: http://localhost:8080
- Admin panel: http://localhost:8080/view/admin/
- Database management: http://localhost:8081 (phpMyAdmin - root/root)

### Manual Setup

1. Prerequisites: PHP 8.1+, MySQL 8.0+, Apache/Nginx, Composer
2. Clone the repository
3. Configure web server to point to project root
4. Create database: `CREATE DATABASE zstyle CHARACTER SET utf8 COLLATE utf8_unicode_ci;`
5. Import schema: `mysql -u root -p zstyle < sources/Zstyle.sql`
6. Update credentials in `sources/model/connectdb.php`
7. Install dependencies: `composer install`

## Running Tests

### All Tests

```bash
# Requires Selenium running for UI tests
docker exec -it zstyle_webserver vendor/bin/phpunit --testdox
```

### Individual Suites

```bash
# Unit tests (no browser needed)
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite Unit --testdox

# API tests (no browser needed)
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite API --testdox

# UI tests (requires Selenium)
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite Functional-UI --testdox

# End-to-end test (requires Selenium)
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite E2E --testdox

# Smoke tests (requires Selenium)
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite Smoke --testdox
```

### Fast Testing (No Selenium)

```bash
# Run only unit and API tests
docker exec -it zstyle_webserver vendor/bin/phpunit --testsuite Unit --testsuite API --testdox
```

### Watch Tests Visually

```bash
# Start Selenium with VNC
docker compose -f docker-compose.selenium.yml up -d

# Connect VNC viewer to localhost:7900 (password: secret)
# Run tests in another terminal to watch execution
```

### Debug and Coverage

```bash
# Verbose output
docker exec -it zstyle_webserver vendor/bin/phpunit --testdox --verbose

# Generate coverage report
docker exec -it zstyle_webserver vendor/bin/phpunit --coverage-html coverage
# Open coverage/index.html in browser

# Run single test
docker exec -it zstyle_webserver vendor/bin/phpunit tests/Unit/CartCalculationTest.php --testdox

# Selenium logs
docker logs -f selenium_chrome
```

## Test Suite

The test suite contains 65 tests organized into 5 categories:

### Unit Tests (23)

Test individual functions and business logic without external dependencies.

- Email, phone, password validation (8 tests)
- Product filtering and utilities (7 tests)
- Cart calculations and totals (8 tests)

Run: `vendor/bin/phpunit --testsuite Unit --testdox`

### API Tests (20)

Test REST endpoints and HTTP responses.

- Authentication endpoints (10 tests)
- Product catalog endpoints (6 tests)
- Shopping cart endpoints (4 tests)

Run: `vendor/bin/phpunit --testsuite API --testdox`

### Functional UI Tests (15)

Test individual UI features using browser automation.

- Login and registration forms (5 tests)
- Product pages and filtering (5 tests)
- Cart operations (5 tests)

Requires Selenium. Run: `vendor/bin/phpunit --testsuite Functional-UI --testdox`

### End-to-End Test (1)

Test the complete shopping journey: browse products → select options → add to cart → checkout → place order.

Requires Selenium. Run: `vendor/bin/phpunit --testsuite E2E --testdox`

### Smoke Tests (5)

Quick verification that critical paths work after deployment.

- Homepage loads
- Login page accessible
- Product page displays
- Cart functions
- Checkout page loads

Requires Selenium. Run: `vendor/bin/phpunit --testsuite Smoke --testdox`

## Application Interface

### Customer Pages

| Page           | Screenshot                         |
| -------------- | ---------------------------------- |
| Home           | ![](screenshot/home.png)           |
| Products       | ![](screenshot/product.png)        |
| Product Detail | ![](screenshot/product-detail.png) |
| Shopping Cart  | ![](screenshot/cart.png)           |
| Checkout       | ![](screenshot/checkout.png)       |
| Login          | ![](screenshot/login.png)          |
| Register       | ![](screenshot/sign-up.png)        |
| Account        | ![](screenshot/profile.png)        |
| Order History  | ![](screenshot/history.png)        |
| News           | ![](screenshot/news.png)           |

### Admin Panel

| Page      | Screenshot                          |
| --------- | ----------------------------------- |
| Dashboard | ![](screenshot/admin-dashboard.png) |
| Products  | ![](screenshot/manager-product.png) |
| Orders    | ![](screenshot/manager-order.png)   |
| Users     | ![](screenshot/manager-user.png)    |
| Vouchers  | ![](screenshot/manager-voucher.png) |
| Comments  | ![](screenshot/manager-comment.png) |

## Docker

### Main Services

`docker-compose.yml` provides:

- **Web Server**: PHP 8.1 + Apache on port 8080
- **Database**: MySQL 8.0 on port 3307
- **Management**: phpMyAdmin on port 8081

Automatically initializes database from `Zstyle.sql` and sets up proper file permissions.

### Selenium Container

`docker-compose.selenium.yml` provides:

- **Browser Automation**: Selenium Chrome standalone server
- **Remote Viewing**: VNC server on port 7900 (password: secret)

Run only when needed for UI testing:

```bash
docker compose -f docker-compose.selenium.yml up -d    # Start
docker compose -f docker-compose.selenium.yml down     # Stop and free ~3GB
```

### Container Commands

```bash
# View running containers
docker ps

# View container logs
docker logs zstyle_webserver
docker logs zstyle_database
docker logs selenium_chrome

# Stop containers
docker compose down
docker compose -f docker-compose.selenium.yml down

# Remove all containers and clean up
docker compose down -v
docker system prune -a
```

## CI/CD

Tests run automatically on GitHub Actions for:

- Push to main or develop branch
- Pull requests to main or develop

Workflow runs: Unit tests → API tests → UI tests → E2E tests → Smoke tests → Coverage report

View results on GitHub in the **Actions** tab. All tests must pass before merging PRs.

## Troubleshooting

**Selenium connection fails**  
Ensure Selenium container is running: `docker compose -f docker-compose.selenium.yml up -d`

**UI tests fail with "element not found"**  
Check if the page loads correctly in VNC. Verify test URL parameters use `pg=` instead of `act=`.

**Database connection error**  
Verify MySQL is running and credentials in `model/connectdb.php` are correct.

**Port already in use**  
Stop conflicting containers or change ports in docker-compose.yml.

## Project Structure

```
sources/
  model/                    # Business logic layer
    connectdb.php          # Database connection
    user.php               # User management
    product.php            # Product functions
    cart.php               # Shopping cart
    donhang.php            # Orders
    ...
  view/                    # Presentation layer
    header.php
    footer.php
    home.php
    product.php
    cart.php
    checkout.php
    login.php
    register.php
    account.php
    news.php
    admin/                 # Admin panel
  tests/                   # Test suite
    Unit/                  # Unit tests (23)
    API/                   # API tests (20)
    UI/                    # UI tests (22)
      LoginUITest.php
      ProductUITest.php
      CartUITest.php
      E2EShoppingFlowTest.php
      SmokeUITest.php
  phpunit.xml              # PHPUnit configuration
  docker-compose.yml       # Main services
  docker-compose.selenium.yml  # Selenium service
  Dockerfile               # PHP container image
  Zstyle.sql              # Database schema
  index.php               # Application router
  mailer.php              # Email handler

PROJECT_FULL_ANALYSIS.md  # Detailed testing analysis
README.md                 # This file
```

## Development

### Adding Features

1. Create a feature branch: `git checkout -b feature/YourFeature`
2. Write tests first (TDD approach recommended)
3. Implement the feature
4. Run all tests: `vendor/bin/phpunit --testdox`
5. Ensure all 65 tests pass
6. Commit and push
7. Create a pull request

### Code Standards

- Test all new functionality
- Update relevant tests when modifying existing code
- Follow MVC architecture pattern
- Use PDO for database queries (prepared statements for security)
- Test coverage should maintain or improve overall metrics

## Testing Methodologies

The test suite employs multiple approaches:

- **Black-box testing**: Validates functionality without examining implementation
- **White-box testing**: Verifies internal logic and code paths
- **Business logic testing**: Tests critical workflows end-to-end (registration → login → purchase)
- **Security testing**: Validates SQL injection prevention, XSS prevention, unauthorized access control
- **Integration testing**: Confirms modules work together (model → controller → view)

## Troubleshooting

### Selenium Connection Issues

```bash
# Verify Selenium is running
docker ps | grep selenium

# Start Selenium if not running
docker compose -f docker-compose.selenium.yml up -d

# Check network connectivity
docker network inspect zstyle_network
```

### UI Test Failures

- Check page loads correctly in VNC (localhost:7900)
- Verify test URL parameters use `pg=` instead of `act=`
- Increase wait times if page loads slowly
- Check Selenium logs: `docker logs -f selenium_chrome`

### Database Connection Error

```bash
# Verify MySQL is running
docker ps | grep zstyle_database

# Check credentials in sources/model/connectdb.php
# Verify database imported: docker exec zstyle_database mysql -u root -p -e "USE zstyle; SHOW TABLES;"
```

### Port Already in Use

Change ports in docker-compose.yml or stop existing containers:

```bash
docker ps
docker stop <container_name>
```

### Disk Space (Selenium)

```bash
# Stop Selenium container (saves ~3GB)
docker compose -f docker-compose.selenium.yml down

# Clean up unused Docker resources
docker system prune -a
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Write tests for new functionality
4. Ensure all 65 tests pass
5. Commit with clear messages
6. Push to your branch
7. Open a pull request

All pull requests must pass CI/CD checks before merging.

## Team

- Phuc linh - Leader
- Cong tuyen
- Quoc Hung
- Van Hau

## Documentation

- [PROJECT_FULL_ANALYSIS.md](PROJECT_FULL_ANALYSIS.md) - Comprehensive testing analysis, methodologies, and test case examples
- [sources/README_TESTING.md](sources/README_TESTING.md) - Detailed automation testing guide and configuration
- [sources/DOCKER_SETUP.md](sources/DOCKER_SETUP.md) - Docker configuration and troubleshooting

## License

MIT License - see LICENSE file for details
