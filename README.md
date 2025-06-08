import os
import shutil
from zipfile import ZipFile
from PIL import Image

# Re-create the Flutter project structure after environment reset
app_dir = "/mnt/data/bees_general_trading"
lib_dir = os.path.join(app_dir, "lib")
assets_dir = os.path.join(app_dir, "assets")

os.makedirs(lib_dir, exist_ok=True)
os.makedirs(assets_dir, exist_ok=True)

# Restore main.dart content
main_dart_code = '''
import 'package:flutter/material.dart';
import 'dart:io';
import 'package:image_picker/image_picker.dart';

void main() {
  runApp(BeesGeneralTradingApp());
}

class BeesGeneralTradingApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Bees General Trading',
      theme: ThemeData(primarySwatch: Colors.amber),
      home: SplashScreen(),
    );
  }
}

class SplashScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Future.delayed(Duration(seconds: 2), () {
      Navigator.pushReplacement(context, MaterialPageRoute(builder: (_) => ProductListPage()));
    });

    return Scaffold(
      body: Container(
        decoration: BoxDecoration(
          image: DecorationImage(
            image: AssetImage('assets/honeycomb.jpg'),
            fit: BoxFit.cover,
          ),
        ),
        child: Center(
          child: Text(
            'Bees General Trading',
            style: TextStyle(
              fontSize: 32,
              color: Colors.white,
              fontWeight: FontWeight.bold,
              shadows: [Shadow(blurRadius: 10, color: Colors.black)]
            ),
          ),
        ),
      ),
    );
  }
}

class Product {
  String name;
  String code;
  int quantity;
  double price;
  String unit;
  String? imagePath;

  Product(this.name, this.code, this.quantity, this.price, this.unit, {this.imagePath});
}

class ProductListPage extends StatefulWidget {
  @override
  _ProductListPageState createState() => _ProductListPageState();
}

class _ProductListPageState extends State<ProductListPage> {
  List<Product> products = [
    Product("BS 2001 IND PUTTUKUDAM", "BS 2001", 73, 26.0, "NOS"),
    Product("BS 2004 IND IDLY POT", "BS 2004", 7, 72.0, "NOS"),
    Product("BS 2011 SAUSPAN 4PCS", "BS 2011", 25, 72.0, "SET"),
  ];

  final picker = ImagePicker();

  void addProduct() async {
    String name = "New Product";
    String code = "NEW001";
    int quantity = 0;
    double price = 0.0;
    String unit = "NOS";
    XFile? image = await picker.pickImage(source: ImageSource.gallery);

    setState(() {
      products.add(Product(name, code, quantity, price, unit, imagePath: image?.path));
    });
  }

  void deleteProduct(int index) {
    setState(() {
      products.removeAt(index);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text("Product List")),
      body: ListView.builder(
        itemCount: products.length,
        itemBuilder: (context, index) {
          final product = products[index];
          return ListTile(
            leading: product.imagePath != null
                ? Image.file(File(product.imagePath!), width: 50, height: 50, fit: BoxFit.cover)
                : Icon(Icons.shopping_bag),
            title: Text(product.name),
            subtitle: Text("Code: ${product.code}\\nQty: ${product.quantity} | Price: ${product.price}"),
            trailing: IconButton(
              icon: Icon(Icons.delete, color: Colors.red),
              onPressed: () => deleteProduct(index),
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: addProduct,
        child: Icon(Icons.add),
      ),
    );
  }
}
'''

# Write main.dart
main_dart_path = os.path.join(lib_dir, "main.dart")
with open(main_dart_path, "w") as f:
    f.write(main_dart_code)

# Create a placeholder honeycomb image
honeycomb_path = os.path.join(assets_dir, "honeycomb.jpg")
img = Image.new('RGB', (800, 600), color=(255, 223, 0))  # yellow background
img.save(honeycomb_path)

# Write pubspec.yaml
pubspec_yaml = """
name: bees_general_trading
description: A mobile product manager for Bees General Trading
version: 1.0.0+1

environment:
  sdk: ">=2.12.0 <3.0.0"

dependencies:
  flutter:
    sdk: flutter
  image_picker: ^1.0.0

flutter:
  uses-material-design: true
  assets:
    - assets/honeycomb.jpg
"""

with open(os.path.join(app_dir, "pubspec.yaml"), "w") as f:
    f.write(pubspec_yaml)

# Copy to a new folder for GitHub packaging
github_repo_dir = "/mnt/data/bees_general_trading_github"
shutil.copytree(app_dir, github_repo_dir)

# Zip it for upload to GitHub
github_zip_path = "/mnt/data/bees_general_trading_github.zip"
shutil.make_archive(github_repo_dir, 'zip', github_repo_dir)

github_zip_path
