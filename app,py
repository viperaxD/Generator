from flask import Flask, request, jsonify
from PIL import Image
import requests
import io

app = Flask(__name__)

def is_valid_url(url):
    try:
        response = requests.head(url)
        response.raise_for_status()
        return True
    except requests.exceptions.RequestException:
        return False

def fetch_image(url):
    response = requests.get(url)
    response.raise_for_status()
    return Image.open(io.BytesIO(response.content)).convert("RGB")

def resize_image(img, size=(32, 32), simplified=False):
    resample_method = Image.Resampling.NEAREST if simplified else Image.Resampling.LANCZOS
    return img.resize(size, resample=resample_method)

@app.route('/convert')
def convert_image():
    image_url = request.args.get('url')

    if not image_url:
        return jsonify({'error': 'URL does not exist'}), 400

    if not is_valid_url(image_url):
        return jsonify({'error': 'Invalid URL'}), 400

    try:
        img = fetch_image(image_url)
        resized_img = resize_image(img, simplified=False)
    except Exception as e:
        return jsonify({'error': f'Resize error: {e}'}), 400

    rgb_values = list(resized_img.getdata())
    return jsonify(rgb_values)

@app.route('/convertsimple')
def convert_image_simple():
    image_url = request.args.get('url')

    if not image_url:
        return jsonify({'error': 'URL does not exist'}), 400

    if not is_valid_url(image_url):
        return jsonify({'error': 'Invalid URL'}), 400

    try:
        img = fetch_image(image_url)
        resized_img = resize_image(img, simplified=True)
    except Exception as e:
        return jsonify({'error': f'Resize error: {e}'}), 400

    rgb_values = list(resized_img.getdata())
    return jsonify(rgb_values)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
