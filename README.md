from flask import Flask, request, jsonify
import re

app = Flask(__name__)

def is_sanitized(input_string):
    # Check for SQL injection characters
    sql_injection_chars = re.compile(r'(\b(?:SELECT|INSERT|UPDATE|DELETE|FROM|WHERE|AND|OR)\b)', re.IGNORECASE)
    return not sql_injection_chars.search(input_string)

@app.route('/v1/sanitized/input/', methods=['POST'])
def check_sanitization():
    try:
        # Parse JSON payload from the request
        payload = request.get_json()

        # Check if 'input' key exists in the payload
        if 'input' not in payload:
            return jsonify({'error': 'Invalid payload. Missing "input" key'}), 400

        input_string = payload['input']

        # Check for SQL injection characters
        if is_sanitized(input_string):
            return jsonify({'result': 'sanitized'})
        else:
            return jsonify({'result': 'unsanitized'})

    except Exception as e:
        return jsonify({'error': 'An error occurred while processing the request', 'details': str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)


