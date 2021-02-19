Requirements
------------

- Java 1.8 (required by Sepia)
- Apache Maven

Building
--------

There is a few options for building this project.

- Multi-module Maven build: This will build the entire project from
  source, including the SimpleRTS and Sepia dependencies. This is
  useful to simply build a working project for use on a new machine.

  Example:

    # Initialize git submodules for dependencies
    git submodule update --init --checkout --force --recursive

    # Build project, setup classpath directory
    mvn -DskipTests=true clean package dependency:copy-dependencies

    # Execute main class
    java -cp 'main/target/example-0.jar:main/target/dependency/*' \
      classname [args] \
      ;

- Individual Maven builds: Used to build and install the modules one
  at a time. This might be necessary to overcome limitations of maven,
  like using -DskipTests=true on some modules (Sepia) but not others
  (main). This is also useful to avoid re-building dependencies for
  every revision of the main module.

  Example:

    # Initialize git submodules for dependencies
    git submodule update --init --checkout --force --recursive

    # Build SimpleRTS and install to local Maven repository
    mvn -f SimpleRTS/pom.xml -DskipTests=true install

    # Build Sepia and install to local Maven repository
    mvn -f Sepia/pom.xml -DskipTests=true install

    # Build main module; do what you need to do as a developer
    mvn -f main/pom.xml clean package dependency:copy-dependencies

    # Maybe execute main class
    java -cp 'main/target/example-0.jar:main/target/dependency/*' \
      classname [args] \
      ;

- Build main Maven module, using prebuilt Sepia: Useful if there are
  undisclosed required changes to the class Sepia distribution.

  Example:

    # Download prebuilt Sepia
    curl -fLO http://engr.case.edu/ray_soumya/sepia/html/_static/Sepia.jar
    sha512sum -c - <<< '77cd28b69d67fb03639a9ed919e30a7818bb7d98fb603bbea9435a6d4ca4f3eb2765581ea8fe7b6daf98b8e9c58a36c60b39e7411883ee1bc2aabd2f3568cbd9 *Sepia.jar'

    # Install prebuilt Sepia to local Maven repository
    mvn install:install-file \
      -Dfile=Sepia.jar \
      -DgroupId=edu.cwru.sepia \
      -DartifactId=Sepia \
      -Dversion=0.10.1 \
      -Dpackaging=jar \
      ;

    # Build main module; do what you need to do as a developer
    mvn -f main/pom.xml clean package dependency:copy-dependencies

    # Maybe execute main class
    java -cp 'main/target/example-0.jar:main/target/dependency/*' \
      classname [args] \
      ;
