from flask import Flask, render_template, request, redirect, url_for
from werkzeug.utils import secure_filename
import os

#predict
import random
from datetime import datetime

def process_image(img_path):
    damage_types = ['Crack', 'Rust', 'Spalling']
    severity = random.choice(['Low', 'Moderate', 'High'])
    return {
        'type': random.choice(damage_types),
        'severity': severity,
        'time': datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    }

def predict_degradation(sensor_value):
    future_risk = sensor_value * random.uniform(1.1, 1.3)
    return round(future_risk, 2)

#sensor_simulator
def get_sensor_data():
    return {
        'strain': round(random.uniform(0.3, 0.8), 2),
        'vibration': round(random.uniform(0.1, 0.5), 2),
        'temperature': round(random.uniform(20, 45), 1)
    }

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'static/uploads'

@app.route('/', methods=['GET', 'POST'])
def dashboard():
    damage_result, forecast = None, None
    sensor_data = get_sensor_data()
    
    if request.method == 'POST':
        file = request.files['file']
        if file:
            filename = secure_filename(file.filename)
            path = os.path.join(app.config['UPLOAD_FOLDER'], filename)
            file.save(path)
            damage_result = process_image(path)
            forecast = predict_degradation(sensor_data['strain'])

    return render_template('dashboard.html', damage=damage_result, forecast=forecast, sensor=sensor_data)

if __name__ == '__main__':
    app.run(debug=True)