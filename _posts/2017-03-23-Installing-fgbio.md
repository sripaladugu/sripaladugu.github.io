#### Installing SBT
1. `wget http://dl.bintray.com/sbt/rpm/sbt-0.13.5.rpm`
2. `rpm2cpio sbt-0.13.5.rpm | cpio -idv`
3. `mkdir sbt-tool`
	1. `mv etc sbt-tool/`
    2. `mv usr sbt-tool/`
4. `module load java`
5. `sbt-tool/usr/bin/sbt --version`
6. append path of `sbt-tool/usr/bin` to your `PATH` variable

#### Building fgbio
1. `git clone https://github.com/fulcrumgenomics/fgbio.git`
2. `cd fgbio`
3. `sbt assembly`
4. Usage:
   (a) `java -jar /isilon/Analysis/research/workspace/sri/dedupComparision/fgbio/target/scala-2.11/fgbio-0.1.4-SNAPSHOT.jar <command>`
