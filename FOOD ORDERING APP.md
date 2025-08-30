```python
# PLEASE DO NOT MODIFY THIS CELL
# RUN THIS CELL BEFORE YOU BEGIN

import unittest
from unittest.mock import patch
```


```python
# PLEASE DO NOT MODIFY THIS CELL
def display_menu():
    """
    Description:  This function when called is to display the food menu and prices.
    """
    #YOUR CODE HERE
    print("Menu:")
    print("1. Pizza - 6500")
    print("2. Burger - 3000")
    print("3. Noodles - 1300")
    print("4. Exit Menu")
```


```python
display_menu()
```

    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu



```python
# PLEASE DO NOT MODIFY THIS CELL
class TestGetUserChoice(unittest.TestCase):
    @patch('builtins.input', side_effect=['2'])
    def test_valid_choice(self, mock_input):
        result = get_user_choice()
        self.assertEqual(result, 2)
    @patch('builtins.input', side_effect=['invalid', '3'])
    def test_invalid_then_valid_choice(self, mock_input):
        result = get_user_choice()
        self.assertEqual(result, 3)

    @patch('builtins.input', side_effect=['5', '4'])
    def test_invalid_then_exit_choice(self, mock_input):
        result = get_user_choice()
        self.assertIsNone(result)

tester = TestGetUserChoice()
tester.test_valid_choice()
tester.test_invalid_then_valid_choice()
tester.test_invalid_then_exit_choice()

```

    Invalid input. Please enter a valid number.
    Invalid choice. Please enter a number between 1 and 4.



```python
def get_quantity():
    """
    Description: Takes user input to get the quantity of the selected food item.
                 Ensures the input is a positive integer.

                 If the input is not an integer return the error 
                 'Invalid input. Please enter a valid number.' 
                 
                 If the input is a negative integer or zero return the error
                 'Quantity must be greater than 0.'
    """
    while True:
        try:
            quantity = int(input("Enter quantity: "))
            if quantity > 0:
                return quantity
            else:
                print("Quantity must be greater than 0.")
        except ValueError:
            print("Invalid input. Please enter a valid number.")

```


```python
# PLEASE DO NOT MODIFY THIS CELL
class TestGetQuantity(unittest.TestCase):
    @patch('builtins.input', side_effect=['3'])
    def test_valid_quantity(self, mock_input):
        result = get_quantity()
        self.assertEqual(result, 3)

    @patch('builtins.input', side_effect=['invalid', '5'])
    def test_invalid_then_valid_quantity(self, mock_input):
        result = get_quantity()
        self.assertEqual(result, 5)

    @patch('builtins.input', side_effect=['0', '-2', '4'])
    def test_non_positive_then_valid_quantity(self, mock_input):
        result = get_quantity()
        self.assertEqual(result, 4)

tester = TestGetQuantity()
tester.test_valid_quantity()
tester.test_invalid_then_valid_quantity()
tester.test_non_positive_then_valid_quantity()

```

    Invalid input. Please enter a valid number.
    Quantity must be greater than 0.
    Quantity must be greater than 0.



```python
def get_item_name(choice):
    """
    Description: Retrieves and returns the name of a food item 
    based on the user's choice number from the menu.
    """
    # YOUR CODE HERE
    item_names = {
        1: "Pizza",
        2: "Burger",
        3: "Noodles"
    }
    return item_names.get(choice)

```


```python
# PLEASE DO NOT MODIFY THIS CELL
assert get_item_name(1) == 'Pizza'
assert get_item_name(2) == 'Burger'
assert get_item_name(3) == 'Noodles'

```


```python
def get_item_price(choice):
    """
    Description: Retrieves and returns the price of a food item based on 
    the user's choice number from the menu.
    """
    # YOUR CODE HERE
    item_prices = {
        1: 6500,
        2: 3000,
        3: 1300
    }
    return item_prices.get(choice)

```


```python
# PLEASE DO NOT MODIFY THIS CELL
assert get_item_price(1) == 6500
assert get_item_price(2) == 3000
assert get_item_price(3) == 1300

```


```python
def calculate_total_price(item_price, quantity):
    """
    Description: Calculates and returns the total price of a specific food item 
    based on its price and the quantity ordered.
    """
    return item_price * quantity

```


```python
# PLEASE DO NOT MODIFY THIS CELL
assert calculate_total_price(5, 2) == 10

```


```python
def place_order():
    cart = {}
    while True:
        display_menu()
        choice = get_user_choice()
        if choice is None:
            break

        item_name = get_item_name(choice)
        item_price = get_item_price(choice)
        quantity = get_quantity()
        total_price = calculate_total_price(item_price, quantity)

        if item_name in cart:
            cart[item_name]['quantity'] += quantity
            cart[item_name]['total_price'] += total_price
        else:
            cart[item_name] = {
                'quantity': quantity,
                'total_price': total_price
            }

    return cart
```


```python
# PLEASE DO NOT MODIFY THIS CELL
class TestPlaceOrder(unittest.TestCase):
    @patch('__main__.get_user_choice', side_effect=[1, 2, None])
    @patch('__main__.get_quantity', return_value=3)
    def test_place_order(self, mock_get_quantity, mock_get_user_choice):
        result = place_order()
        expected_result = {
            'Pizza': {'quantity': 3, 'total_price': 19500},
            'Burger': {'quantity': 3, 'total_price': 9000}
        }
        self.assertEqual(result, expected_result)
        self.assertEqual(mock_get_user_choice.call_count, 3)
        self.assertEqual(mock_get_quantity.call_count, 2)

tester = TestPlaceOrder()
tester.test_place_order()

```

    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu
    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu
    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu



```python
def check_out(cart):
    """
    Description: Finalizes the order by displaying the contents of the shopping cart, including quantities and total prices.
                 Prints the total order price like a receipt.

                 The reciept would look like this if the cart is empty
                 
                     Your cart is empty. No items to check out.


                 If the Cart is has items in it then the receipt should look exactly like this

                     Checking out...
                     Your order details:
                     Item 1: Quantity - 2, Total Price - 2000
                     Item 2: Quantity - 3, Total Price - 1500
                     Total Order Price: 3500
                     Thank you for ordering!
    """
    # YOUR CODE HERE
    if not cart:
        print("Your cart is empty. No items to check out.")
        return

    print("Checking out...")
    print("Your order details:")
    total_order_price = 0
    item_num = 1

    for item, details in cart.items():
        print(f"Item {item_num}: Quantity - {details['quantity']}, Total Price - {details['total_price']}")
        total_order_price += details['total_price']
        item_num += 1

    print(f"Total Order Price: {total_order_price}")
    print("Thank you for ordering!")

```


```python
# PLEASE DO NOT MODIFY THIS CELL
class TestCheckOut(unittest.TestCase):
    @patch('builtins.print')
    def test_check_out_empty_cart(self, mock_print):
        cart = {}
        check_out(cart)
        mock_print.assert_called_with("Your cart is empty. No items to check out.")

    @patch('builtins.print')
    def test_check_out_non_empty_cart(self, mock_print):
        cart = {
            'Item 1': {'quantity': 2, 'total_price': 20},
            'Item 2': {'quantity': 3, 'total_price': 15}
        }
        check_out(cart)

        expected_output_1 = [
            "Checking out...",
            "Your order details:",
            "Item 1: Quantity - 2, Total Price - $20",
            "Item 2: Quantity - 3, Total Price - $15",
            "Total Order Price: $35",
            "Thank you for ordering!"
        ]

        expected_output_2 = [
            "Checking out...",
            "Your order details:",
            "Item 1: Quantity - 2, Total Price - 20",
            "Item 2: Quantity - 3, Total Price - 15",
            "Total Order Price: 35",
            "Thank you for ordering!"
        ]

        calls_1 = [unittest.mock.call(output) for output in expected_output_1]
        calls_2 = [unittest.mock.call(output) for output in expected_output_2]

        try:
            mock_print.assert_has_calls(calls_1, any_order=False)
        except AssertionError:
            mock_print.assert_has_calls(calls_2, any_order=False)

tester = TestCheckOut()
tester.test_check_out_empty_cart()
tester.test_check_out_non_empty_cart()

```


```python
def food_ordering_app():
    """
    Description: The main function that initiates the food ordering application.
                 Calls place_order() to build the shopping cart and then calls check_out() to complete the order.

                 NOTE THAT IF ANY OF THE OTHER FUNCTIONS ARE NOT CORRECTLY WRITTEN THIS WILL FAIL
                 PLEASE DO NOT MODIFY THIS CELL
    """
    #YOUR CODE HERE
    print("Welcome to the Food Ordering App!")
    cart = place_order()
    check_out(cart)

```


```python
food_ordering_app()
```

    Welcome to the Food Ordering App!
    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu


    Enter your choice (1-4):  1
    Enter quantity:  2


    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu


    Enter your choice (1-4):  3
    Enter quantity:  2


    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu


    Enter your choice (1-4):  2
    Enter quantity:  3


    Menu:
    1. Pizza - 6500
    2. Burger - 3000
    3. Noodles - 1300
    4. Exit Menu


    Enter your choice (1-4):  4


    Checking out...
    Your order details:
    Item 1: Quantity - 2, Total Price - 13000
    Item 2: Quantity - 2, Total Price - 2600
    Item 3: Quantity - 3, Total Price - 9000
    Total Order Price: 24600
    Thank you for ordering!



```python
# PLEASE DO NOT MODIFY THIS CELL
class TestFoodOrderingApp(unittest.TestCase):
    @patch('builtins.print')
    @patch('__main__.place_order', return_value={'Item 1': {'quantity': 2, 'total_price': 20}})
    @patch('__main__.check_out')
    def test_food_ordering_app(self, mock_check_out, mock_place_order, mock_print):
        food_ordering_app()
        mock_print.assert_called_with("Welcome to the Food Ordering App!")
        mock_place_order.assert_called_once()
        mock_check_out.assert_called_once()

tester = TestFoodOrderingApp()
tester.test_food_ordering_app()

```


```python

```
