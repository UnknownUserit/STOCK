# STOCK
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.preprocessing import MinMaxScaler
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import LSTM
#change location as per req
training_set=pd.read_csv('/content/drive/MyDrive/deep_learning_dataset/google_stock/google_train.csv')
training_set
#run 
training_set.info()
#run
plt.figure(figsize=(5, 5))
plt.subplots_adjust(top=1.25, bottom=1.2)
training_set['Adj Close'].plot()
plt.ylabel('Adj Close')
plt.xlabel(None)
plt.title(f"Closing Price of Google")
plt.tight_layout()
#run
plt.figure(figsize=(15, 20))
plt.subplots_adjust(top=1.25, bottom=1.2)
training_set['Volume'].plot()
plt.ylabel('Volume')
plt.xlabel(None)
plt.title(f"Sales Volume")
#run
training_set=training_set.iloc[:,1:2].values
#run
sc= MinMaxScaler()
training_set=sc.fit_transform(training_set)
X_train= training_set[0:1257]
y_train= training_set[1:1258]
X_train=np.reshape(X_train, (1257 , 1 , 1))
#run
regressor = Sequential()
regressor.add(LSTM(units=4, activation= 'sigmoid', input_shape= (None,1)))
regressor.add(Dense( units=1 ))
regressor.compile(optimizer='adam', loss='mean_squared_error')
regressor.fit(X_train, y_train, batch_size=32, epochs=200)
#run
test_set = pd.read_csv('/content/drive/MyDrive/deep_learning_dataset/google_stock/google_test.csv')
real_stock_price = test_set.iloc[:,1:2].values
inputs = real_stock_price
inputs = sc.transform(inputs)
inputs = np.reshape(inputs, (20 , 1, 1))
predicted_stock_price = regressor.predict(inputs)
predicted_stock_price = sc.inverse_transform(predicted_stock_price)
#run 
plt.plot( real_stock_price , color = 'red' , label = 'Real Google Stock Price')
plt.plot( predicted_stock_price , color = 'blue' , label = 'Predicted Google Stock Price')
plt.title('Google Stock Price Prediction')
plt.xlabel( 'time' )
plt.ylabel( 'Google Stock Price' )
plt.legend()
plt.show()
