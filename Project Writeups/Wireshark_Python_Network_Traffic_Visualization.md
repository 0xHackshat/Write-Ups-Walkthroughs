# Wireshark Python Network Traffic Visualization

**Repository:** [kingakshat/Wireshark-Python-Network-Traffic-Visualization](https://github.com/kingakshat/Wireshark-Python-Network-Traffic-Visualization)  
**Current repository owner/path:** `0xHackshat/Wireshark-Python-Network-Traffic-Visualization`

## Project Overview

The Wireshark-Python-Network-Traffic-Visualization project offers a unique approach to analyzing network traffic by providing users with an immersive visual representation of their network activities using Google Earth. This visualization is facilitated by a Python script that extracts data from Wireshark, a widely used network protocol analyzer, and integrates it into the program to generate the final visualization.

## Languages and Utilities Used

- Python
- Wireshark
- Google Earth
- Geolitecity Database

## Program Walk-through

The program guides users through the process of visualizing network traffic data on Google Earth.

1. **Data Extraction:** Extracts relevant network traffic data from Wireshark captures.

   ![Wireshark capture extraction](image/wireshark_01.png)

2. **Visualization Generation:** Using Google Earth and the geographical information obtained from the Geolitecity database, the program generates an immersive visual representation of the network traffic.

   ![Google Earth visualization](image/wireshark_02.jpg)

3. **Interpretation:** Users can interact with the visualization to analyze network activities, identify patterns, and gain insights into the network's behavior.

   ![Traffic visualization result](image/wireshark_03.jpeg)

## Repository Files

The repository listing includes:

- `GeoLiteCity-data/`
- `static/`
- `README.md`
- `main.py`
- `output.kml`
- `wire.pcap`

## Main Script (`main.py`)

The script reads a packet capture (`wire.pcap`), extracts source and destination IP addresses, looks up geographic coordinates using the GeoLiteCity database, and writes KML placemarks for display in Google Earth.

```python
import dpkt
import socket
import pygeoip

gi = pygeoip.GeoIP('GeoLiteCity-data/GeoLiteCity.dat')

def main():
    f = open('wire.pcap', 'rb')
    op = open('output.kml', 'w')
    pcap = dpkt.pcap.Reader(f)

    kmlheader = '<?xml version="1.0" encoding="UTF-8"?> \\n<kml xmlns="http://www.opengis.net/kml/2.2">\\n<Document>\\n'\\
        '<Style id="transBluePoly">' \\
        '<LineStyle>' \\
        '<width>1.5</width>' \\
        '<color>501400E6</color>' \\
        '</LineStyle>' \\
        '</Style>'

    kmlfooter = '</Document>\\n</kml>\\n'
    kmldoc = kmlheader + plotIPs(pcap) + kmlfooter
    print(kmldoc)
    op.writelines(kmldoc)
    op.close()

def plotIPs(pcap):
    kmlPts = ''
    for (ts, buf) in pcap:
        try:
            eth = dpkt.ethernet.Ethernet(buf)
            ip = eth.data
            src = socket.inet_ntoa(ip.src)
            dst = socket.inet_ntoa(ip.dst)
            KML = retKML(dst, src)
            kmlPts = kmlPts + KML
        except:
            pass
    return kmlPts

def retKML(dstip, srcip):
    dst = gi.record_by_name(dstip)
    src = gi.record_by_name('103.6.186.150')
    # src = gi.record_by_name('xxx.xxx.xxx.xxx')

    try:
        dstlongitude = dst['longitude']
        dstlatitude = dst['latitude']
        srclongitude = src['longitude']
        srclatitude = src['latitude']

        kml = (
            '<Placemark>\\n'
            '<name>%s</name>\\n'
            '<extrude>1</extrude>\\n'
            '<tessellate>1</tessellate>\\n'
            '<styleUrl>#transBluePoly</styleUrl>\\n'
            '<LineString>\\n'
            '<coordinates>%6f,%6f\\n%6f,%6f</coordinates>\\n'
            '</LineString>\\n'
            '</Placemark>\\n'
        ) % (dstip, dstlongitude, dstlatitude, srclongitude, srclatitude)
        return kml
    except:
        return ''

if __name__ == '__main__':
    main()
```

## Contribution

Contributions and feedback are welcome! If you have suggestions for improvement, found errors, or want to contribute additional features, feel free to create pull requests or raise issues.

## Disclaimer

This project is developed for educational and research purposes only. Ensure you have appropriate authorization before using it for any other purposes.

## Connect with Us

Feel free to connect with us on GitHub to learn more about the project and our contributions to network analysis and visualization.

**Original repository:** [GitHub](https://github.com/kingakshat/Wireshark-Python-Network-Traffic-Visualization)
