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
            subtitle: Text("Code: ${product.code}\nQty: ${product.quantity} | Price: ${product.price}"),
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