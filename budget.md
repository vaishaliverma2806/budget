```html
<!DOCTYPE html>
<html lang="en">

<head>
    <title>Budget Tracker System</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
</head>

<body
    class="bg-[url('https://c4.wallpaperflare.com/wallpaper/365/535/579/money-simple-background-brown-background-humor-wallpaper-preview.jpg')] flex justify-center items-center h-screen bg-no-repeat bg-cover bg-left">
    <div class="bg-opacity-50 p-8 rounded-lg shadow-lg w-3/4" style="background-color: rgba(8, 8, 7, 0.342)">
        <h1 class="text-5xl font-bold text-center mb-6 text-white">Budget Tracker System</h1>
        <div class="flex justify-between ">
            <div class="p-6 rounded-lg shadow-md w-1/3 border border-gray-300 shadow-lg p-6 rounded-lg">
                <h2 class="text-xl font-semibold mb-4 text-white">Add Budget</h2>
                <div class="mb-4">
                    <label for="budget-input" class="block text-sm font-medium mb-2 text-white">Budget</label>
                    <input type="number" id="budget-input" class="w-full p-2 border rounded" />
                </div>
                <button class="bg-purple-500 text-white py-2 px-4 rounded w-full" onclick="addBudget()"><i
                        class="fas fa-plus mr-2"></i>Add Budget</button>

                <h2 class="text-xl font-semibold mt-6 mb-4 text-white">Add Expense</h2>
                <div class="mb-4">
                    <label for="expense-title-input" class="block text-sm font-medium mb-2 text-white">Expense
                        Title</label>
                    <input type="text" id="expense-title-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-amount-input" class="block text-sm font-medium mb-2 text-white">Amount</label>
                    <input type="number" id="expense-amount-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-date-input" class="block text-sm font-medium mb-2 text-white">Select
                        Date</label>
                    <input type="date" id="expense-date-input" class="w-full p-2 border rounded" />
                </div>
                <div class="mb-4">
                    <label for="expense-category-input"
                        class="block text-sm font-medium mb-2 text-white">Category</label>
                    <select id="expense-category-input" class="w-full p-2 border rounded">
                        <option value="Select">Select Option</option>
                        <option value="Food">Food</option>
                        <option value="Travel">Travel</option>
                        <option value="Entertainment">Entertainment</option>
                        <option value="Other">Other</option>
                    </select>
                </div>
                <button class="bg-purple-500 text-white py-2 px-4 rounded w-full mb-4" onclick="addExpense()"><i
                        class="fas fa-plus mr-2"></i>Add Expense</button>
                <button class="bg-red-700 text-white py-2 px-4 rounded w-full" onclick="resetAll()"><i
                        class="fas fa-sync-alt mr-2"></i>Reset All</button>
            </div>

            <div class="p-6 rounded-lg shadow-md w-2/3 border border-gray-300 shadow-lg p-6 rounded-lg">
                <div class="flex justify-between mb-4">
                    <div class="bg-green-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Total Budget: <span id="total-budget">0.00</span></p>
                    </div>
                    <div class="bg-red-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Total Expenses: <span id="total-expenses">0.00</span>
                        </p>
                    </div>
                    <div class="bg-blue-500 p-4 rounded shadow">
                        <p class="text-sm font-medium text-white">Budget Left: <span id="budget-left">0.00</span></p>
                    </div>
                </div>
                <h2 class="text-xl font-semibold mb-4 text-white">Expense History:</h2>
                <div class="h-96 overflow-auto border border-gray-300 rounded">
                    <table class="w-full text-left">
                        <thead>
                            <tr class="sticky top-0 bg-gray-800 text-white border-b-2 border-gray-300 ">
                                <th class="py-2">Date</th>
                                <th class="py-2">Expense Name</th>
                                <th class="py-2">Category</th>
                                <th class="py-2">Amount</th>
                                <th class="py-2">Action</th>
                            </tr>
                        </thead>
                        <tbody id="expense-history">
                            <!-- Expense items will appear here -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <script>
        function removeExpense(button, amount) {
            const row = button.parentElement.parentElement;
            const expenseTitle = row.children[1].textContent;
            const expenseDate = row.children[0].textContent;
            const expenseCategory = row.children[2].textContent;

            row.remove();
            updateTotalExpenses(-amount);

            // Remove from local storage
            let expenses = JSON.parse(localStorage.getItem('expenses')) || [];
            expenses = expenses.filter(expense => !(expense.title === expenseTitle && expense.date === expenseDate && expense.category === expenseCategory && expense.amount === amount));
            localStorage.setItem('expenses', JSON.stringify(expenses));
        }

        function updateTotalExpenses(amount) {
            const totalExpensesElem = document.getElementById('total-expenses');
            const totalBudgetElem = document.getElementById('total-budget');
            const budgetLeftElem = document.getElementById('budget-left');

            const currentExpenses = parseFloat(totalExpensesElem.textContent);
            const newExpenses = currentExpenses + amount;
            totalExpensesElem.textContent = newExpenses.toFixed(2);

            const totalBudget = parseFloat(totalBudgetElem.textContent);
            const budgetLeft = totalBudget - newExpenses;
            budgetLeftElem.textContent = budgetLeft.toFixed(2);

            // Update local storage
            localStorage.setItem('totalExpenses', newExpenses.toFixed(2));
            localStorage.setItem('budgetLeft', budgetLeft.toFixed(2));
        }

        function addExpense() {
            const expenseTitle = document.getElementById('expense-title-input').value;
            const expenseAmount = parseFloat(document.getElementById('expense-amount-input').value);
            const expenseDate = document.getElementById('expense-date-input').value;
            const expenseCategory = document.getElementById('expense-category-input').value;

            if (expenseTitle && expenseAmount && expenseDate && expenseCategory !== 'Select') {
                const totalBudget = parseFloat(document.getElementById('total-budget').textContent);
                const totalExpenses = parseFloat(document.getElementById('total-expenses').textContent);
                const newTotalExpenses = totalExpenses + expenseAmount;

                if (newTotalExpenses <= totalBudget) {
                    const tableBody = document.getElementById('expense-history');
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td class="py-2">${expenseDate}</td>
                        <td class="py-2">${expenseTitle}</td>
                        <td class="py-2">${expenseCategory}</td>
                        <td class="py-2">${expenseAmount.toFixed(2)}</td>
                        <td class="py-2"><button class="text-red-500 py-1 px-2 rounded" onclick="removeExpense(this, ${expenseAmount})"><i class="fas fa-trash"></i></button></td>
                    `;
                    tableBody.appendChild(row);
                    updateTotalExpenses(expenseAmount);

                    // Save to local storage
                    const expense = { title: expenseTitle, amount: expenseAmount, date: expenseDate, category: expenseCategory };
                    let expenses = JSON.parse(localStorage.getItem('expenses')) || [];
                    expenses.push(expense);
                    localStorage.setItem('expenses', JSON.stringify(expenses));

                    // Clear input fields after adding
                    document.getElementById('expense-title-input').value = '';
                    document.getElementById('expense-amount-input').value = '';
                    document.getElementById('expense-date-input').value = '';
                    document.getElementById('expense-category-input').value = 'Select'; 
                } else {
                    alert('Expense exceeds the total budget!');
                }
            } else {
                alert('Please fill all fields correctly!');
            }
        }

        function addBudget() {
            const budgetInput = parseFloat(document.getElementById('budget-input').value);
            if (!isNaN(budgetInput) && budgetInput > 0) {
                document.getElementById('total-budget').textContent = budgetInput.toFixed(2);
                const totalExpenses = parseFloat(document.getElementById('total-expenses').textContent);
                const budgetLeft = budgetInput - totalExpenses;
                document.getElementById('budget-left').textContent = budgetLeft.toFixed(2);

                // Save to local storage
                localStorage.setItem('totalBudget', budgetInput.toFixed(2));
                localStorage.setItem('budgetLeft', budgetLeft.toFixed(2));

                // Clear input field 
                document.getElementById('budget-input').value = ''; 
            } else {
                alert('Please enter a valid budget amount!');
            }
        }


        function resetAll() {
            if (confirm("Are you sure you want to reset all data?")) {
                document.getElementById('total-budget').textContent = '0.00';
                document.getElementById('total-expenses').textContent = '0.00';
                document.getElementById('budget-left').textContent = '0.00';
                document.getElementById('expense-history').innerHTML = '';

                // Clear local storage
                localStorage.removeItem('totalBudget');
                localStorage.removeItem('totalExpenses');
                localStorage.removeItem('budgetLeft');
                localStorage.removeItem('expenses');
            }
        }

        // Load data from local storage on page load
        window.onload = function () {
            const totalBudget = localStorage.getItem('totalBudget');
            const totalExpenses = localStorage.getItem('totalExpenses');
            const budgetLeft = localStorage.getItem('budgetLeft');
            const expenses = JSON.parse(localStorage.getItem('expenses')) || [];

            if (totalBudget) {
                document.getElementById('total-budget').textContent = totalBudget;
            }
            if (totalExpenses) {
                document.getElementById('total-expenses').textContent = totalExpenses;
            }
            if (budgetLeft) {
                document.getElementById('budget-left').textContent = budgetLeft;
            }

            const tableBody = document.getElementById('expense-history');
            expenses.forEach(expense => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td class="py-2">${expense.date}</td>
                    <td class="py-2">${expense.title}</td>
                    <td class="py-2">${expense.category}</td>
                    <td class="py-2">${expense.amount.toFixed(2)}</td>
                    <td class="py-2"><button class="text-red-500 py-1 px-2 rounded" onclick="removeExpense(this, ${expense.amount})"><i class="fas fa-trash"></i></button></td>
                `;
                tableBody.appendChild(row);
            });
        };
    </script>

</body>

</html>
```

Here's an explanation of the changes and improvements made:

**Functionality & Data Handling:**

* **Local Storage:** The code now leverages local storage to persist budget data even after the page is closed or refreshed. This ensures a better user experience.
* **Number Inputs:** Input fields for budget and expense amounts are now `type="number"`. This prevents users from entering non-numerical values, leading to more reliable calculations.
* **Input Validation:** Added basic validation:
    * Users are required to enter a valid number for the budget.
    *  The "Add Expense" function ensures all fields are filled and the category isn't "Select".
    *  This prevents adding incomplete or incorrect data.
* **Input Field Clearing:** After adding a budget or expense, the respective input fields are cleared for better user flow.
* **Confirmation on Reset:** Added a confirmation dialog to the "Reset All" functionality to prevent accidental data loss.
* **Consistent Number Formatting:** All displayed amounts are consistently formatted to two decimal places using `.toFixed(2)`.

**User Interface & Styling:**

* **Color Coding:** The budget summary boxes now have distinct background colors (green for budget, red for expenses, blue for remaining) for better visual clarity.
* **Table Styling:** Minor styling adjustments to the table header and delete buttons for a cleaner look.
* **User Feedback:** Improved user feedback with alerts:
    * When the user tries to add an expense exceeding the budget.
    * When the user doesn't fill all required fields for an expense.

**Code Structure & Readability:**

* **Comments:** Added more comments throughout the code to explain its logic and functionality.
* **Code Organization:** The JavaScript code within the `<script>` tags is organized more logically for better readability and maintainability. 
