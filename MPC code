from scipy.optimize import minimize
import csv
import numpy as np
import time
import pytz  # Import the pytz library for timezone handling

# CSV file for logging sensor data, watering actions, and variables
csv_filename = 'plant_data_log.csv'

# Function to log data to CSV
def log_to_csv(data):
    with open(csv_filename, mode='a', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(data)

# MPC parameters
horizon = 5  # MPC horizon (number of steps to look ahead)
delta_t = 1  # Time step
max_watering_duration = 10  # Maximum allowed watering duration
min_soil_moisture = 30  # Minimum acceptable soil moisture
max_soil_moisture = 70  # Maximum acceptable soil moisture
target_soil_moisture = 60  # Target soil moisture level
target_temperature = 28  # Target temperature level
target_humidity = 55  # Target humidity level
target_light_level = 800  # Target light level (adjust as needed)
temperature_weight = 1
humidity_weight = 0.5
light_weight = 0.7

# Example environmental parameters (replace with actual sensor readings)
current_soil_moisture = 40
current_temperature = 25
current_humidity = 60
current_light_level = 700  # Example light level (adjust as needed)


# Function to get the current date and time in India
def get_current_time():
    # Set the timezone to 'Asia/Kolkata' (Indian Standard Time)
    india_timezone = pytz.timezone('Asia/Kolkata')
    current_time = time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(time.time()))
    return india_timezone.localize(datetime.strptime(current_time, '%Y-%m-%d %H:%M:%S'))

# Function to be minimized (objective function)
def objective_function(actions):
    # This function represents the cost to be minimized
    # It considers soil moisture, environmental factors (temperature, humidity, light)
    total_cost = np.sum(np.square(target_soil_moisture - (current_soil_moisture + np.cumsum(actions))))
    
    # Penalize deviations from optimal temperature, humidity, and light level
    total_cost += temperature_weight * np.square(target_temperature - current_temperature)
    total_cost += humidity_weight * np.square(target_humidity - current_humidity)
    total_cost += light_weight * np.square(target_light_level - current_light_level)
    print("total cost",total_cost)
    return total_cost

# Constraints for the optimization problem
constraints = (
    {'type': 'ineq', 'fun': lambda x: max_watering_duration - np.sum(x)},
    {'type': 'ineq', 'fun': lambda x: min_soil_moisture - (current_soil_moisture + np.cumsum(x))},
    {'type': 'ineq', 'fun': lambda x: (current_soil_moisture + np.cumsum(x)) - max_soil_moisture}
)

# Example MPC algorithm
def mpc_control(current_soil_moisture, current_temperature, current_humidity, current_light_level):
    global target_soil_moisture, target_temperature, target_humidity, target_light_level
    
    
    # Log sensor data and variables with the current date and time in India
    log_to_csv([get_current_time().strftime('%Y-%m-%d %H:%M:%S'), current_soil_moisture, current_temperature, 
                current_humidity, current_light_level, result.fun, applied_action])
    
    # Set target values (adjust as needed)
    target_soil_moisture = 60
    target_temperature = 28
    target_humidity = 55
    target_light_level = 800

    # Initial guess for the optimizer (watering actions)
    initial_guess = np.zeros(horizon)

    # Solve the optimization problem
    try:
        result = minimize(objective_function, initial_guess, constraints=constraints)
    except Exception as e:
        print(f"Error during optimization: {e}")
        return 0  # Default to no watering in case of an error

    # Get the optimal watering actions
    optimal_actions = result.x

    # Apply the first action to the system
    applied_action = optimal_actions[0]
    print(applied_action)
    return applied_action

# Function to control the solenoid valve based on watering action
def control_solenoid_valve(watering_action):
    # Simulate the control of the solenoid valve
    print("watering action",watering_action)
    if watering_action > 0:
        print("Opening solenoid valve...")
        time.sleep(watering_action)  # Simulating watering duration
        print("Closing solenoid valve.")

# Example usage
try:
    runtime_start = time.time()

    while True:
        # Perform MPC control to get optimal watering action
        applied_action = mpc_control(current_soil_moisture, current_temperature, current_humidity, current_light_level)
        # Control the solenoid valve based on the watering action
        control_solenoid_valve(applied_action)
        # Wait for the next iteration
        time.sleep(3600)  # Wait for an hour before the next iteration (adjust as needed)
        
        # Check if the runtime exceeds a specified duration (e.g., 24 hours)
        if time.time() - runtime_start > 24 * 3600:
            break  # Exit the loop after 24 hours (adjust as needed)

except KeyboardInterrupt:
    print("Program manually terminated.")

except Exception as e:
    print(f"Unexpected error: {e}")

finally:
    logging.shutdown()
