from flask import Flask, render_template, request, redirect, url_for

app = Flask(__name__)

# In-memory data storage (replace with a database for real applications)
patients = {}
next_patient_id = 1

@app.route('/')
def index():
    return render_template('index.html', patients=patients)

@app.route('/add', methods=['GET', 'POST'])
def add_patient():
    if request.method == 'POST':
        global next_patient_id
        name = request.form['name']
        age = request.form['age']
        gender = request.form['gender']
        diagnosis = request.form['diagnosis']
        
        patients[next_patient_id] = {
            'id': next_patient_id,
            'name': name,
            'age': age,
            'gender': gender,
            'diagnosis': diagnosis
        }
        next_patient_id += 1
        return redirect(url_for('index'))
    return render_template('add_patient.html')

@app.route('/view/<int:patient_id>')
def view_patient(patient_id):
    patient = patients.get(patient_id)
    if patient:
        return render_template('view_patient.html', patient=patient)
    return "Patient not found", 404

@app.route('/edit/<int:patient_id>', methods=['GET', 'POST'])
def edit_patient(patient_id):
    patient = patients.get(patient_id)
    if not patient:
        return "Patient not found", 404

    if request.method == 'POST':
        patient['name'] = request.form['name']
        patient['age'] = request.form['age']
        patient['gender'] = request.form['gender']
        patient['diagnosis'] = request.form['diagnosis']
        return redirect(url_for('index'))
    return render_template('edit_patient.html', patient=patient)

@app.route('/delete/<int:patient_id>')
def delete_patient(patient_id):
    if patient_id in patients:
        del patients[patient_id]
        return redirect(url_for('index'))
    return "Patient not found", 404

if __name__ == '__main__':
    app.run(debug=True)
