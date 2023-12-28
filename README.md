# insurwave
Interview test
# The Insurwave code test

## Scenario 1: API Automated Testing
Write an API automation script using Python/.NET to test a RESTful API endpoint (https://www.weatherapi.com/ using the free account)
* Implement automated tests for at least 3 endpoints which Include GET and POST requests and verify response status codes, data correctness, and data type.
Note: Handle error cases gracefully.
*  Modify your API tests to include parameterisation for different test data, so that you can iterate test data from a CSV/Excel file:

Scenario 2: Data Validation
Implement data validation in your API tests. Verify the API response against a schema or JSON schema (e.g Use a popular library or tool for schema validation)

base_url = "https://api.weatherapi.com/v1"
api_key = "c68c53bfaa7d4e469d2230659231912"

# Function to perform GET request and verify response
def test_get_request(endpoint, params=None):
    url = f"{base_url}/{endpoint}"
    response = requests.get(url, params=params)

    # Validate status code
    assert response.status_code == 200, f"GET request failed with status code {response.status_code}"

    # Validate response data correctness and data type (modify as per your API response structure)
    json_data = response.json()
    assert 'location' in json_data, "Location data not found in the response"
    assert isinstance(json_data['location'], dict), "Location should be a dictionary"

    print(f"GET request successful for endpoint: {endpoint}")

# Function to perform POST request and verify response
def test_post_request(endpoint, data):
    url = f"{base_url}/{endpoint}"
    headers = {"Content-Type": "application/json"}
    response = requests.post(url, json=data, headers=headers)

    # Validate status code
    assert response.status_code == 200, f"POST request failed with status code {response.status_code}"

    # Validate response data correctness and data type (modify as per your API response structure)
    json_data = response.json()
    assert 'location' in json_data, "Location data not found in the response"
    assert isinstance(json_data['location'], dict), "Location should be a dictionary"

    print(f"POST request successful for endpoint: {endpoint}")

# Example usage
if __name__ == "__main__":
    # Example GET request for current weather
    test_get_request("current.json", params={"key": api_key, "q": "London"})

    # Example POST request for forecast
    test_post_request("forecast.json", data={"key": api_key, "q": "New York"})

    # Example GET request for historical weather
    test_get_request("history.json", params={"key": api_key, "q": "Berlin", "dt": "2023-01-01"})

    # Example handling of error case (invalid endpoint)
    test_get_request("invalid_endpoint", params={"key": api_key, "q": "Paris"})

Test cases to create:
\\Validate the API keys for minimum and maximum range/length
\\Verify that these keys are answering the call requests if you are using JSON or XML APIs in your application

\\Verify the Parse response to parse out portions of the response data returned from the server
\\Check the JSON Schema validation, the Field Type, and the Mandatory Fields
\\Validate the Response headers and Negative Testcases response

Identify and verify the handling of API error codes - 

Client-Side Status Codes -
\\400 Bad Request, 403 Forbidden, 401 Unauthorized, 429 Too Many Requests

Server-Side Status Codes - 
\\500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timed Out, 501 etc

Validate Response payload to determine their format and readability factor
\\Test request chaining to identify the working of multiple APIs together

Verify APIs with input parameters.
\\Validate end-to-end CRUD (create, read, update, and delete) flow for application API

Check database integrity test cases - 

\\Checking whether or NOT a blank value or default value can be retrieved from the database.

\\Validating each value if it is successfully saved to the database.

\\Ensuring the data compatibility against old hardware or old versions of operating systems.

\\Verifying the data in data tables can be modified and deleted

\\Running data tests for all data files, including clip art, tutorials, templates, etc.


Scenario 3: UI Automated Testing
Write a UI automation script using Selenium and Python/.NET to test https://www.saucedemo.com/ website or any other e-commerce website of your choice.

Automate at least 3 test scenarios, such as login, product search, and adding an item to the cart along with Implementation of assertions to verify expected UI elements. You should demonstrate test setup and teardown methods.
Note: Handle synchronisation issues effectively.

Implement the Page Object Model for the e-commerce website and refactor your previous UI tests to use the POM structure.

class BaseTest(unittest.TestCase):
    def setUp(self):
        self.driver = webdriver.Chrome()
        self.driver.maximize_window()
        self.driver.get("https://www.saucedemo.com/")

    def tearDown(self):
        self.driver.quit()

class LoginPage(BaseTest):
    def test_login(self):
        login_page = LoginPageObject(self.driver)
        login_page.login("standard_user", "Password_1")

        # Assertion: Check if the login is successful
        self.assertTrue(ProductPageObject(self.driver).is_on_product_page(), "Login failed")

class ProductPage(BaseTest):
    def test_product_search(self):
        login_page = LoginPageObject(self.driver)
        login_page.login("standard_user", "Password_1")

        product_page = ProductPageObject(self.driver)
        product_page.search_product("Sauce Labs Backpack")

        # Assertion: Check if the product is displayed
        self.assertTrue(product_page.is_product_displayed("Sauce Labs Backpack"), "Product not found")

    def test_add_to_cart(self):
        login_page = LoginPageObject(self.driver)
        login_page.login("standard_user", "Password_1")

        product_page = ProductPageObject(self.driver)
        product_page.add_to_cart("Sauce Labs Backpack")

        # Assertion: Check if the item is added to the cart
        self.assertTrue(product_page.is_item_added_to_cart("Sauce Labs Backpack"), "Item not added to the cart")

# Page Object for Login Page
class LoginPageObject:
    def __init__(self, driver):
        self.driver = driver
        self.username_input = By.ID, "user-name"
        self.password_input = By.ID, "password"
        self.login_button = By.ID, "login-button"

    def login(self, username, password):
        self.driver.find_element(*self.username_input).send_keys(username)
        self.driver.find_element(*self.password_input).send_keys(password)
        self.driver.find_element(*self.login_button).click()

# Page Object for Product Page
class ProductPageObject:
    def __init__(self, driver):
        self.driver = driver
        self.product_search_input = By.ID, "search-box"
        self.add_to_cart_button = By.XPATH, "//div[text()='Sauce Labs Backpack']/following-sibling::div/button"
        self.cart_indicator = By.XPATH, "//div[@id='shopping_cart_container']//span"

    def is_on_product_page(self):
        return "inventory.html" in self.driver.current_url

    def search_product(self, product_name):
        self.driver.find_element(*self.product_search_input).send_keys(product_name)

    def is_product_displayed(self, product_name):
        WebDriverWait(self.driver, 10).until(
            EC.text_to_be_present_in_element((By.CLASS_NAME, "inventory_item_name"), product_name))
        return product_name in self.driver.page_source

    def add_to_cart(self, product_name):
        self.driver.find_element(*self.add_to_cart_button).click()

    def is_item_added_to_cart(self, product_name):
        WebDriverWait(self.driver, 10).until(
            EC.text_to_be_present_in_element(self.cart_indicator, "1"))
        return "1" in self.driver.find_element(*self.cart_indicator).text

if __name__ == "__main__":
    unittest.main()

Scenario 4: UI Frameworks

ui_test_framework/
|-- tests/
|   |-- __init__.py
|   |-- test_login.py
|-- pages/
|   |-- __init__.py
|   |-- login_page.py
|-- utils/
|   |-- __init__.py
|   |-- config.py
|   |-- logger.py
|-- conftest.py
|-- pytest.ini
|-- run_tests.py

# ui_test_framework/utils/config.py
import configparser

class Config:
    config = configparser.ConfigParser()
    config.read('config.ini')

    @staticmethod
    def get_browser():
        return Config.config.get('TestConfig', 'browser')

    @staticmethod
    def get_base_url():
        return Config.config.get('TestConfig', 'base_url')
# ui_test_framework/utils/logger.py
import logging

class Logger:
    @staticmethod
    def get_logger(name):
        logger = logging.getLogger(name)
        logger.setLevel(logging.INFO)

        formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(message)s')

        console_handler = logging.StreamHandler()
        console_handler.setFormatter(formatter)
        logger.addHandler(console_handler)

        return logger
  # ui_test_framework/utils/config.ini
[TestConfig]
browser = chrome
base_url = https://www.saucedemo.com/

# ui_test_framework/pytest.ini
[pytest]
addopts = -v -s --html=reports/report.html

# ui_test_framework/pages/login_page.py
from selenium.webdriver.common.by import By

class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        self.username_input = By.ID, "user-name"
        self.password_input = By.ID, "password"
        self.login_button = By.ID, "login-button"

    def login(self, username, password):
        self.driver.find_element(*self.username_input).send_keys(username)
        self.driver.find_element(*self.password_input).send_keys(password)
        self.driver.find_element(*self.login_button).click()

# ui_test_framework/tests/test_login.py
import pytest
from ui_test_framework.pages.login_page import LoginPage
from ui_test_framework.utils.config import Config
from ui_test_framework.utils.logger import Logger
from selenium import webdriver

logger = Logger.get_logger(__name__)

@pytest.fixture(scope="function")
def setup_teardown():
    logger.info("Setting up the test")
    driver = webdriver.Chrome()  # or webdriver.Firefox()
    driver.maximize_window()
    yield driver
    logger.info("Tearing down the test")
    driver.quit()

def test_login(setup_teardown):
    driver = setup_teardown
    logger.info("Running the test")

    base_url = Config.get_base_url()
    driver.get(base_url)

    login_page = LoginPage(driver)
    login_page.login("standard_user", "secret_sauce")

    assert "inventory.html" in driver.current_url




