### Case Study: Subscription Analytics and Recommendations
# Objective:
# Perform advanced data analysis on customer subscriptions, focusing on:
# 1. Enumerating subscription plans and customers.
# 2. Utilizing comprehensions to filter and transform subscription data.
# 3. Zipping lists to align customer IDs with subscription plans and revenues.
# 4. Applying functions and lambda functions to calculate statistics and derive insights.

# Step 1: Define the raw data for customers and their subscriptions.
customer_ids = [101, 102, 103, 104, 105, None, 107, 108, "109", 110]
subscription_plans = ["Basic", "Premium", "Premium", "Basic", "Gold", "Gold", None, "Basic", "Basic", "Gold"]
monthly_revenue = [15.0, 45.0, 45.0, 15.0, 70.0, 70.0, "N/A", 15.0, 15.0, -10.0]
active_status = [True, True, False, True, True, True, False, True, "yes", True]  # Mixed and inconsistent data

# Step 2: Clean the dirty data.
def clean_data(customer_ids, subscription_plans, monthly_revenue, active_status):
    cleaned_data = []
    for i in range(len(customer_ids)):
        try:
            customer_id = int(customer_ids[i]) if customer_ids[i] else None
            plan = subscription_plans[i] if subscription_plans[i] in ["Basic", "Premium", "Gold"] else None
            revenue = float(monthly_revenue[i]) if isinstance(monthly_revenue[i], (int, float)) and float(monthly_revenue[i]) > 0 else None
            active = True if str(active_status[i]).lower() in ["true", "yes"] else False
            if customer_id and plan and revenue is not None:
                cleaned_data.append((customer_id, plan, revenue, active))
        except (ValueError, TypeError):
            continue
    return cleaned_data

cleaned_data = clean_data(customer_ids, subscription_plans, monthly_revenue, active_status)
print("\nCleaned Data:")
print(cleaned_data)

# Step 3: Enumerate the cleaned data to index customers with their details.
enumerated_data = list(enumerate(cleaned_data))
print("\nEnumerated Cleaned Data:")
for index, details in enumerated_data:
    print(f"Index: {index}, Details: {details}")

# Step 4: Use a list comprehension to filter only active subscriptions.
active_customers = [customer for index, customer in enumerated_data if customer[3]]
print("\nActive Customers:")
print(active_customers)

# Step 5: Use a dictionary comprehension to group customers by their subscription plans.
customers_by_plan = {
    plan: [customer[0] for customer in active_customers if customer[1] == plan]
    for plan in set(subscription_plans) if plan is not None
}
print("\nCustomers Grouped by Plan:")
print(customers_by_plan)

# Step 6: Calculate total revenue for active subscriptions using a comprehension.
total_active_revenue = sum([customer[2] for customer in active_customers])
print("\nTotal Active Revenue:", total_active_revenue)

# Step 7: Zip customer IDs with revenue for further analysis.
customer_revenue_mapping = list(zip([customer[0] for customer in active_customers], [customer[2] for customer in active_customers]))
print("\nCustomer Revenue Mapping:")
print(customer_revenue_mapping)

# Step 8: Reuse analytics functions on cleaned data.
def calculate_average_revenue(customers):
    revenues = [customer[2] for customer in customers]
    return sum(revenues) / len(revenues) if revenues else 0

def high_revenue_customers(customers, threshold):
    return [customer for customer in customers if customer[2] > threshold]

average_revenue = calculate_average_revenue(active_customers)
print("\nAverage Revenue from Active Customers:", average_revenue)

high_revenue_threshold = 30.0
high_revenue_active_customers = high_revenue_customers(active_customers, high_revenue_threshold)
print("\nHigh Revenue Active Customers:")
print(high_revenue_active_customers)

# Step 9: Generate insights for inactive customers from cleaned data.
inactive_customers = [customer for customer in enumerated_data if not customer[1][3]]
print("\nInactive Customers:")
print(inactive_customers)

# Step 10: Reanalyze churn rate and visualize cleaned data.
def calculate_churn_rate(total_customers, active_customers):
    inactive_count = total_customers - len(active_customers)
    churn_rate = (inactive_count / total_customers) * 100
    return round(churn_rate, 2)

total_customers = len(cleaned_data)
churn_rate = calculate_churn_rate(total_customers, active_customers)
print(f"\nChurn Rate: {churn_rate}%")

import matplotlib.pyplot as plt

def plot_revenue_distribution(customers):
    revenues = [customer[2] for customer in customers]
    plt.hist(revenues, bins=5, color='blue', alpha=0.7)
    plt.title("Revenue Distribution of Active Customers (Cleaned)")
    plt.xlabel("Revenue")
    plt.ylabel("Frequency")
    plt.show()

plot_revenue_distribution(active_customers)

# Step 11: Predict future revenue based on cleaned data.
def predict_future_revenue(current_revenue, growth_rate, months):
    return round(current_revenue * ((1 + growth_rate / 100) ** months), 2)

growth_rate = 5.0  # Assuming a 5% monthly growth rate
future_revenue = predict_future_revenue(total_active_revenue, growth_rate, 12)
print(f"\nPredicted Revenue for Next 12 Months: ${future_revenue}")
