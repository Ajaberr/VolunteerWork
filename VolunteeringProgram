import numpy
from PIL import Image
from pathlib import Path
import re
from datetime import datetime
import os
from google.cloud import vision
import io
import pygsheets

# Initialize the Google Sheets client
#gsheets_client = pygsheets.authorize(service_file=r'')

# Set the environment variable to the path of your service account JSON key file for Google Vision API
#os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = r''

# Initialize the Google Vision client
vision_client = vision.ImageAnnotatorClient()


code = [
    'A', 'AA', 'AC', 'AJ',  # Rabat
    'AB', 'AE', 'AY', 'AS',  # Salé
    'AD',  # Témara
    'B', 'BA', 'BB', 'BE', 'BH', 'BJ', 'BK', 'BL', 'BM', 'BF', 'BV', 'BW',  # Casablanca
    'BX', 'DF', 'PK', 'PP', 'PS',  # Moroccans residing abroad (MRE)
    'C', 'CC', 'CD',  # Fez
    'CB',  # Sefrou
    'CN',  # Boulemane
    'D', 'DN',  # Meknes
    'DA',  # Azrou
    'DB',  # Ifrane
    'DC',  # Moulay Idriss Zerhoun
    'DJ',  # Ain Taoujdate
    'DN',  # El Hajeb
    'DO',  # Ouislane
    'E', 'EE',  # Marrakesh
    'EA',  # Ben Guerir
    'F',  # Oujda
    'FA',  # Berkane
    'FB',  # Taourirt
    'FC',  # El Aioun Sidi Mellouk
    'FD',  # Ain Bni Mathar
    'FE',  # Saïdia
    'FG',  # Figuig
    'FH',  # Jerada
    'FJ',  # Ahfir
    'FK',  # Touissit
    'FL',  # Bouarfa
    'G',  # Kenitra, Sidi Yahya El Gharb
    'GA',  # Sidi Slimane, Sidi Yahya El Gharb
    'GB',  # Souk El Arbaa
    'GK',  # Sidi Kacem
    'GM',  # Ouazzane
    'GN',  # Mechra Bel Ksiri
    'GJ',  # Jorf El Melha
    'H', 'HH',  # Safi
    'HA',  # Youssoufia
    'I',  # Beni Mellal
    'IA',  # Kasba Tadla
    'IB',  # Fquih Ben Saleh
    'IC',  # Azilal
    'ID',  # Souk Sebt Ould Nemma
    'IE',  # Demnate
    'J', 'JK',  # Agadir
    'JA',  # Guelmim
    'JB',  # Inezgane, Dcheira El Jihadia
    'JC',  # Taroudant
    'JD',  # Sidi Ifni
    'JE',  # Tiznit
    'JF',  # Tan-Tan
    'JH',  # Chtouka Aït Baha
    'JM',  # Aït Melloul, Temsia, Lqliâa, Oulad Dahou
    'JT',  # Oulad Teima
    'JY',  # Tata
    'JZ',  # Assa-Zag
    'K', 'KB',  # Tangier
    'KA',  # Asilah
    'L',  # Tétouan
    'LA',  # Larache
    'LB',  # Ksar el-Kebir
    'LC',  # Chefchaouen
    'LE',  # Martil
    'LF',  # Fnideq
    'LG',  # M'diq
    'M',  # El Jadida
    'MA',  # Azemmour
    'MC',  # Sidi Bennour
    'MD',  # Zemamra
    'N',  # Essaouira
    'O', 'OD',  # Dakhla
    'P',  # Ouarzazate
    'PA',  # Tinghir
    'PB',  # Zagora
    'Q',  # Khouribga
    'QA',  # Oued Zem
    'R',  # Al Hoceima
    'RB',  # Imzouren
    'RC',  # Targuist
    'RX',  # Bni Bouayach
    'S', 'SA',  # Nador
    'SH',  # Laayoune
    'SJ',  # Smara
    'SK',  # Tarfaya
    'SL',  # Boujdour
    'T',  # Mohammedia
    'TA', 'TK',  # Benslimane
    'U',  # Errachida
    'UA',  # Goulmima
    'UB',  # Er-Rich
    'UC',  # Erfoud
    'UD',  # Rissani
    'V',  # Khenifra
    'VA',  # Midelt, Itzer
    'VM',  # M'rirt
    'W',  # Settat
    'WA',  # Berrechid
    'WB',  # Ben Ahmed
    'X',  # Khemisset
    'XA',  # Tifelt
    'Y',  # Kalaat Sraghna
    'Z',  # Taza
    'ZG',  # Guercif
    'ZH',  # Karia Ba Mohamed
    'ZT',  # Taounate
]
numbers = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"]

def google_vision_ocr(image_path):
    with io.open(image_path, 'rb') as image_file:
        content = image_file.read()

    image = vision.Image(content=content)
    response = vision_client.text_detection(image=image)  # Use vision_client here
    texts = response.text_annotations

    if response.error.message:
        raise Exception(f"Error during text detection: {response.error.message}")

    return texts[0].description if texts else ""

def process_image(image_path):
    address = ""
    result = google_vision_ocr(image_path)
    result = result.split()  

    print(result)

    
    date_pattern = re.compile(r'\d{1,2}[/.]\d{1,2}[/.]\d{4}')
    date_of_birth = ""
    
    for word in result:
        match = date_pattern.match(word)
        if match:
            date_of_birth = match.group()
            date_of_birth = date_of_birth.replace('.', '/')
            dob_datetime = datetime.strptime(date_of_birth, '%d/%m/%Y')
            if dob_datetime <= datetime.today():
                break

   

   
    for i, word in enumerate(result):
        if word == 'à' or word == '*':  
            print("Found 'à' in result.")
            if i + 1 < len(result):
                address += result[i + 1] + " " 
            if i + 2 < len(result):
                address += result[i + 2] + " "
            if i + 3 < len(result):
                address += result[i + 3] + " "
            break  




    ID = ""
    for i in result:
        if len(i) > 3:
            first2 = i[0] + i[1]
            for c in code:
                if (c.lower() == first2.lower() and i[2] in numbers) or (c.lower() == i[0].lower() and i[1] in numbers):
                    ID = i
                    break

 
    phonenumber = "Not Found"
    for word in result:
        if len(word) > 7:
            if ((word[:2] in ["06", "07", "05", "08"] and len(word) == 10) or (word[:3] == "212" and len(word) == 12)) and word.isdigit():
                phonenumber = word



 

    return {
        "Address": address,
        "ID": ID,
        "Phone Number": phonenumber,
        "DOB": date_of_birth
    }

# Open the Google Sheets document and work with the sheet
sh = gsheets_client.open('Volunteering')
wks = sh.sheet1
wks.update_value('B1', "Address")
wks.update_value('C1', "ID")
wks.update_value('D1', "Phone Number")
wks.update_value('E1', "DOB")

# Process images and update the sheet
# image_dir = Path(r'folerdirectory/images')

for i, image_file in enumerate(image_dir.iterdir()):
    if image_file.suffix.lower() in ['.jpg', '.jpeg', '.png']:
        results = process_image(image_file)
        wks.update_value("B" + str(i+2), results["Address"])
        wks.update_value("C" + str(i+2), results["ID"])
        wks.update_value("D" + str(i+2), results["Phone Number"])
        wks.update_value("E" + str(i+2), results["DOB"])
        print(results)


# NOTE: PHONENUMBER HAS TO BE WRITTEN ON PAPER TO SCAN WITH CARD, AND SCANNER SHOULD SCAN AT HIGH QUALITY OR THIS CODE WILL NOT WORK!
