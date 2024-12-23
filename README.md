# Crude Oil Prices Forecasting App Documentation

## ***Introduction***
This app forecasts crude oil prices based on historical data using the Prophet model and provides visualizations of the forecasted data. Users can specify the number of days to forecast, and the app generates predictions along with visual plots.

Additionally, the app uses ngrok to tunnel the Streamlit app to the public internet for easy access.

## ***Features***
* Crude Oil Price Forecasting: The app uses historical data to predict future crude oil prices.
Interactive Forecasting: Users can specify the number of days (1 to 365) to forecast using a sidebar input.
* Forecast Plot: The app visualizes the forecasted crude oil prices using interactive Plotly plots.
* Forecast Components: The app provides components of the forecast, such as trends and seasonalities.
* Tunneling with ngrok: The app uses ngrok to create a secure tunnel to the local Streamlit server, making it accessible via a public URL.
App Flow
* Load the Forecasting Model:
The app starts by loading a pre-trained Prophet model for crude oil price forecasting. This model is stored in a .pkl file.

* User Input for Forecasting:
The user can input the number of days (between 1 and 365) they want to forecast using a Streamlit sidebar. Once the user clicks the "Forecast" button, the app generates the future data and forecasts the crude oil prices for the specified period.

## ***Display Forecast Results:***

* Forecasted Data: Displays the forecasted crude oil prices in a table.
* Forecast Plot: Visualizes the forecast using Plotly for interactive plotting.
* Forecast Components: Plots the individual components of the forecast, such as seasonal effects.
* Tunneling with ngrok:

* Environment Setup: Loads the ngrok authentication token from the .env file.
* Start ngrok Tunnel: Uses the pyngrok library to create a secure HTTP tunnel for the local Streamlit app on port 8501.
* Public URL: The app prints the public URL generated by ngrok, allowing the app to be accessed from anywhere on the web.

  ## ***Click the URL to view a video of how the model works***

[Click here to download & view video](https://github.com/Marxie-ops/crude_oil_web_hosting_via-ngrok/blob/main/streamlit-app-2024-12-21-10-12-79.webm)

## ***Conclusion***

This app uses Prophet, a powerful forecasting tool, to predict future crude oil prices based on historical data. It provides users with a simple interface where they can input the number of days for which they want to generate forecasts. The app also visualizes the forecasted data, along with trend components like seasonality and holidays.

By integrating ngrok, the app is made accessible remotely via a public URL, allowing users to interact with the forecasting model from anywhere. Whether you're a researcher, analyst, or enthusiast, this app offers a convenient way to explore crude oil price trends and make informed predictions based on past performance.



***App Code Explanation***

```python
%%writefile app.py
import streamlit as st
import joblib
import pandas as pd
from prophet import Prophet
from prophet.plot import plot_plotly

# Title
st.title("Crude Oil Prices Forecasting App")
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

# Load the model
model = joblib.load('Crude_Prices_forecasting_model.pkl')

# User Input for Forecasting
st.sidebar.header("Forecasting Parameters")
n_days = st.sidebar.number_input("Enter the number of days to forecast:", min_value=1, max_value=365, value=30)

# Generate future data
if st.button("Forecast"):
    future = model.make_future_dataframe(periods=n_days)
    future['shale_oil_impact'] = ((future['ds'] >= '2015-01-01') & (future['ds'] <= '2019-12-31')).astype(int)
    future['biden_policy_shift'] = ((future['ds'] >= '2021-01-20') & (future['ds'] <= '2024-12-31')).astype(int)

    forecast = model.predict(future)

    # Display forecast
    st.subheader("Forecasted Data")
    st.write(forecast)

    # Plot forecast
    st.subheader("Forecast Plot")
    fig = plot_plotly(model, forecast)
    st.plotly_chart(fig)

    # Plot components
    st.subheader("Forecast Components")
    components_fig = model.plot_components(forecast)
    st.pyplot(components_fig)

# Install required packages
!pip install python-dotenv
from dotenv import load_dotenv
import os
from pyngrok import ngrok

# Load the .env file
load_dotenv('.env')

# Access the token
ngrok_token = os.getenv('NGROK_AUTH_TOKEN')
print(f"Ngrok Token: {ngrok_token}")

# Kill any existing ngrok process
!pkill -f ngrok

# Set ngrok token and connect
ngrok.set_auth_token(ngrok_token)

# Define tunnel configuration
tunnel_config = {
    "proto": "http",  # Tunnel protocol
    "addr": 8501,     # Local port
}

# Create tunnel and get public URL
public_url = ngrok.connect(**tunnel_config)

# Print the public URL for the app
print(f"Streamlit app is live at: {public_url}")



