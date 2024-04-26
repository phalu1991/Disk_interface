from flask import Flask, render_template
import os

app = Flask(__name__)
# Path of the directory 
app.template_folder = os.path.join(os.path.dirname(os.path.abspath('C:\\Users\\phalg\\PycharmProjects\\Python_Practice\\templates')), 'templates')

@app.route('/')
def index():
    # Assuming test_results is a list or dictionary containing test results
    test_results = ['Pass']  # Replace [...] with your actual test results

    # Pass test_results to the template using the render_template function
    return render_template('dashboard.html', test_results=test_results)

if __name__ == '__main__':
    app.run(debug=True)
