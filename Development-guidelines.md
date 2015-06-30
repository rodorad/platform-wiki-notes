# Platform development guidelines

## Coding style

### Java
You should use coding style imported from here: [google-styleguide](https://code.google.com/p/google-styleguide/source/browse/trunk/eclipse-java-google-style.xml?r=84).
There might be some problems with import, to latest Eclipse. You might need to manually set the indentation length to four spaces.
This file works perfectly in IntelliJ Idea with [this plugin] (https://plugins.jetbrains.com/plugin/6546?pr=idea) installed.

### Python
Follow the standard [PEP8] (https://www.python.org/dev/peps/pep-0008/) style. Pycharm (which is basically Idea IDE with a Python plugin) checks for PEP8 compliance by default.
The only thing, you should do differently than PEP8 recommends are docstrings. Format them using [reStructuredText] (https://www.python.org/dev/peps/pep-0287/) style, like in example below:
    
    class ExampleClass():
        
        """
        This docstring says about the role of this class.
        """

        def example_method(self, count):
            """
            This method creates a list of consecutive numbers of the given length.
            :param count: Length of the requested list.
            :type count: int
            :returns: A list of consecutive numbers, starting from 0.
            :rtype: list[int]
            :raises ValueError: When count is less than zero.
            """
            if count < 0:
                raise ValueError('count can't be lesser than zero')
            return [i for i in range(count)]

**Remember: you should document every public method!**

PyCharm generates stubs for those types of docstrings. It is enough to type """ or ''' after a function or a class declaration and press Enter or Space.

## Unit testing
* Use one unit test per one tested state according to: (http://blog.8thlight.com/uncle-bob/2013/09/23/Test-first.html)
* Use following naming convention: `testedFunctionName_testedState_expectedResult` or `checkToken_tokenInvalid_returnFalse`
	Read more about this concept on blog: (http://osherove.com/blog/2005/4/3/naming-standards-for-unit-tests.html)
* Use meaningful asserts rather than "assertThat(result, is(expected))", and write good assert messages
* Do not use field injection - inject into constructor instead, so there will be no need to mock private fields etc.

## Swagger
Swagger is a simple but powerful representation of your RESTful API.
Look for http:/[app.address]/sdoc.jsp to see app endpoints.
Hint: If your want to enable swagger for your app, avoid using swagger with spring-boot and jetty, it might not work. Use tomcat instead. 

## License Maven Plugin and pre-commit git hooks
License Maven Plugin is a commandline tool which allow us to change both the style and content of license headers in our sources files.  
If you are working on java projects there should be a `pom.xml` file in the root directory. In that case you can simply use the tool from terminal:
* mvn license:check - checking do all of our sources files have a proper headers
* mvn license:remove - removing headers 
* mvn license:format - adding headers

If you aren't working on java projects (bash, go, scala...), there should be a `license_checker.xml` file in the license_checker directory. In this case use:
* mvn -f license_checker/license_checker.xml license:remove

Adding a functionality which check if our files have a proper headers while committing and reject that commit if it hasn't:
* open project_root_directory/.git/hooks
* find a pre-commit.sample file and rename it to pre-commit
* for java project you should change the content like this:
```
  #!/bin/sh
  cd license
  ./header_check.sh
```
* for other languages change cd/license to cd/license_checker
 
## Additional reading:
* [[FAQs]]
* [Microservices Architecture for Spring on Cloud Foundry] (https://docs.google.com/document/d/15G8ew0qEDqpuBTWH9YGHKhda6HaLvfKuS4pnB-CPm50/mobilebasic).
* Microservices and the [principles of 12 factor app](http://12factor.net/).
* [Richardson Maturity Model] (http://martinfowler.com/articles/richardsonMaturityModel.html).
* [CloudFoundry documentation] (https://docs.cloudfoundry.org/).
* [example CloudFoundry app - Spring Music] (https://github.com/cloudfoundry-samples/spring-music)
* Single Page App
* Oauth2, JWT token
* Hadoop, MapReduce
* Apache Spark