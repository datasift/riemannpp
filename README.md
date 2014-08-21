# riemannpp (C++)

## Introduction

C++ client library for [Riemann](https://github.com/aphyr/riemann).

This client library was inspired by [Riemann C client library](https://github.com/algernon/riemann-c-client).

Features:
* Sending events over TCP and UDP
* Launching queries (TCP only)
* Support for tags and attributes on events
* Ability to send multiple events in a single message
* Convenient API
* Test suite

## Installation

If you're a developer, Riemann uses [Google Protocol Buffers](http://code.google.com/p/protobuf/), so make sure that's installed and available on your system path.

The library uses autotools along with a dependency on the [Riemann C client library](https://github.com/algernon/riemann-c-client). First install that library (instructions in README.md), then continue with the following commands to build and install riemannpp:

```bash
$ git clone git://github.com/bigdatadev/riemannpp.git
$ cd riemannpp
$ autoreconf -i
$ ./configure && make && make check && make install
```

## Getting Started

Here is a simple example of how to connect to a Riemann server using tcp and send an event:

```cpp
#include <riemannpp/riemannpp.hpp>
#include <tuple>

namespace rpp = riemannpp;
using namespace std;

int main() {
	try {
		rpp::client client(rpp::client_type::tcp, "localhost", 5555);
		
		rpp::event e;
		e << make_tuple(rpp::event_field::host,    "localhost")
		  << make_tuple(rpp::event_field::service, "demo-client")
		  << make_tuple(rpp::event_field::state,   "ok");
		client << e;
	} catch (rpp::internal_exception &e) {
		std::cerr << "Error: " << e.error() << " - " << e.reason() << "." << std::endl;
		exit (EXIT_FAILURE);
	}
	return (EXIT_SUCCESS);
}
```

You can receive events in a similar way:

```cpp
std::unique_ptr<rpp::message> rcv;
client >> rcv;

// print all the events
for (auto& e : rcv->get_events()) {
	std::cout << e << std::endl;
}
```

This can be useful if you want to run a query:

```cpp
rpp::query q("service = \"superdooper\"");
client << q;
```

You can also send multiple events:

```cpp
rpp::message m;
rpp::event e1;
e1 << make_tuple(rpp::event_field::host,    "localhost")
   << make_tuple(rpp::event_field::service, "demo-client-memory")
   << make_tuple(rpp::event_field::state,   "critical");
rpp::event e2;
e2 << make_tuple(rpp::event_field::host,    "localhost")
   << make_tuple(rpp::event_field::service, "demo-client-cpu")
   << make_tuple(rpp::event_field::state,   "ok");
m << e1 << e2;
client << m;
```

## Contributing

Just send me a pull request. Please take a look at the project issues and see how you can help. Here are some tips:
- please add more tests.
- please check your syntax.

## Author

Christopher Gilbert

* Web: [http://cjgilbert.me](http://cjgilbert.me)
* Twitter: [@bigdatadev](https://twitter.com/bigdatadev)
* Linkedin: [/in/christophergilbert](https://www.linkedin.com/in/christophergilbert)

## Copyright

See [LICENSE](LICENSE) document