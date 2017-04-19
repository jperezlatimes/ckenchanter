# ckenchanter
A [PyEnchant](http://pythonhosted.org/pyenchant/tutorial.html) extension for CKEditor's [SCAYT plugin](http://ckeditor.com/addon/scayt)

## How to use it
This module extends PyEnchant's SpellChecker class to communicate with CKEditor's built in SCAYT plugin. To begin you'll need to add the following lines to your CKEditor `config.json` file. Update the values to reflect your app settings.

```JavaScript
config.scayt_autoStartup = true;
config.scayt_servicePath = '/spellcheck/';
config.scayt_serviceHost = 'localhost';
config.scayt_servicePort = '8005';
config.scayt_serviceProtocol = 'http';
```

The `config.scayt_servicePath` endpoint should submit the request data to a CKEnchanter object and return it's response. Here is an example Flask route:

```Python
from ckenchanter import CKEnchanter

@views.route('/spellcheck/', methods=['POST'])
def spellcheck():
    """
    Sends a line of text to the ckenchanter module
    """
    cke = CKEnchanter()
    response = cke.parse_ckeditor_request(request.data)
    return jsonify(response)
```

By default CKEnchanter uses PyEnchant's 'en_US' dictionary. If you wish to use a Personal Word List that is compatible with PyEnchant, you can give CKEnchanter the path to the text file as an argument. CKEnchanter will be expanded to use different language dictionaries in the future.

```Python
import os
from ckenchanter import CKEnchanter

@views.route('/spellcheck/', methods=['POST'])
def spellcheck():
    """
    Sends a line of text to the ckenchanter module
    """
    file_dir = os.path.dirname(__file__)
    word_list_path = os.path.join(file_dir, "dicts/mywords.txt")
        
    cke = CKEnchanter(word_list_path)
    response = cke.parse_ckeditor_request(request.data)
    return jsonify(response)
```

That's it! CKEnchanter will extract the text from CKEditor's request string and run it through PyEnchant, returning a list of suggestions formatted for SCAYT. Enjoy!
