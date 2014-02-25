Copyright (c) 2012 Dmitry Pankratov

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

Type-safe thin C++ wrapper over cJSON library (header-only).
Version 0.4

When compiled with recent gcc compiler with c++11 support the following features are enabled:
* initializer list for array object construction
* default template parameters for constructor, get<> and asArray<> functions
* std::shared_ptr

if C++11 support is not compiled the std::tr1::shared_ptr implementation is used.
Feel free to replace it with boost::shared_ptr if needed (see below _SHARED_PTR_IMPL macro)

Usage examples:
	// parse and get value
	JSONObject obj = cjsonpp::parse(jsonstr);

	// JSONObject can be copied over

	// get value of the named element
	try {
		std::cout << obj.get<int>("intval") << std::endl;
		std::cout << obj.get<JSONObject>("intval").as<int>() << std::endl;
	} catch (const JSONError& e) {
		std::cerr << e.what() << '\n';
	}

	// get array
	std::vector<double> arr1 = obj.get("elems").asArray<double>();
	std::list<std::string> arr2 = obj.get("strs").asArray<std::string, std::list>();

	...
	// construct object
	JSONObject obj;
	std::vector<int> v = {1, 2, 3, 4}; // c++11 only
	obj.set("intval", 1234);
	obj.set("arrval", v);
	obj.set("doubleval", 100.1);
	obj.set("nullval", cjsonpp::nullObject());

	...
	// another way of constructing array
	JSONObject arr = cjsonpp::arrayObject();
	arr.add("s1");
	arr.add("s2");
	JSONObject obj;
	obj.set("arrval", arr);
	std::cout << obj << std::endl;

The following data types are supported with get<>("name") and as<>() functions:
* int
* int64_t
* double
* std::string
* bool
* JSONObject

To add support for more data types add a template specialization for private static function as<>(cJSON*).
Example:
	QString JSONObject::as<QString>(cJSON* obj)
	{
		return QString::fromStdString(as<std::string>(obj));
	}