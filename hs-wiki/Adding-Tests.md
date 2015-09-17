This document is part of the [Hydroshare Developers' Guide](https://github.com/hydroshare/hydroshare2/wiki/Hydroshare-Developers'-Guide).

# Adding tests

After you have [created a Hydroshare app](https://github.com/hydroshare/hydroshare2/wiki/Customizing-Hydroshare#wiki-working-on-your-own-hydroshare-extension), you may want to create tests to ensure your functionality runs properly. In more complex applications, testing each component "by hand" may take an extended period of time, so you may wish to write tests to automate this process.

To this end, Django provides a testing suite. It can be found in the `tests` directory of the code base. Django's documentation contains a guide for how to [write and run your own tests](https://docs.djangoproject.com/en/dev/topics/testing/overview/).

If you need additional help, Django documentation also comes with a [tutorial for creating automated tests](https://docs.djangoproject.com/en/dev/intro/tutorial05/), which is the fifth part in a series of tutorials on how to [write a Django app](https://docs.djangoproject.com/en/dev/intro/tutorial01/).

### Where and how to write a test

To find the code that you want to test, look in `hs_core/hydroshare/*` and `hs_core/views/`.

Write your tests in `hs_core/test/api`. Begin your filename with `test_`. This is required.

All tests should inherit from `django.test.testcase` (so that when you run tests, it finds them and knows what to do with them).

### Tips on writing and running tests

**If your test fails,** look at the stack trace or error that comes out, and use your judgment to decide if the test is failing because of an error in the test, rather than errors in the code. It is important to write tests so that it is clear why the test failed.

**Even if your app is in `hs/(name of your app)`,** you will want to use:

    manage.py test (name of your app)

Instead of:

    manage.py test hs.(name of your app)

This differs from in settings.py and namespaces, where apps are referred to as `hs.(name of your app)`.