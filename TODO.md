* Figure out if emtools/corpreport works and what it does (can I stop using grdreport?)
* Fix mrintel error where it things characterName is an int
* Update price data to use CCP feed
    * this may be unnecessary since eve-central probably reports that back out for us still
* Update to new SDE
    * Rhea 1.0 (Dec 2014)
        * nothing relevant
    * Crius 1.0 (Jul 2014)
        * removed ramTypeRequirements and ramAssemblyLines
            * fixed in dev/scot/crius
        * moved invBlueprintTypes to YAML
            * fixed in dev/scot/crius
        * Arkady says I shouldn't rely on third-party conversions because eventually those people stop playing and stop updating their conversion, but I think I'll probably use Steve Ronuken's anyway, because waiting for myself to do the conversion is even less reliable...he has a psql conversion now but probably case-sensitive
        * I can convert Steve's dump to the psql-friendly lowercase names with a vi regex
            * first pg_restore it to the text version ("pg_restore postgres-latest.dmp > postgres-latest-text.dmp")
            * :%s/"\([a-w,y-z]\a*\u\a*\)"/\1/g
            * that's "strip the quotes off any quoted sequence of letters that starts with a lowercase letter other than x and has at least one uppercase letter"
                * all the identifiers we care about match this, and it avoids all the non-identifier quoted strings I could find in the content
                * the "at least one uppercase letter" works because Steve's conversion doesn't quote identifiers that are already all lowercase
                * the "other than x" silliness is because xMin and xMax need to be kept quoted, fortunately they're the only things that have an x right after a quote
            * then import to psql ("psql dbname < postgres-latest-text.dmp")
        * added cost multiplier columns to ramAssemblyLineType*
            * presumably we need to factor this in, unless it's included in the live cost data I'm already getting from the API in 051f8c6291207fecef20e6bdcc67b63cbaddd37f
    * Rubicon 1.2 (Feb 2014)
        * moved dbo.map* to MySQL (universeDataDx.db)
            * this is probably not a huge deal yet since the old data is probably still mostly right
            * mrintel uses
                * mapsolarsystems
                * mapsolarsystemjumps
                * mapregions
                * mapconstellations
                * mapdenormalize
                * maplocationwormholeclasses
            * gradient/uploader uses
                * mapsolarsystems
                * mapconstellations
                * mapregions
            * gradient/dbutils.py uses
                * mapsolarsystems
                * mapconstellations
                * mapregions
                * mapsolarsystemjumps
            * emtools/intel/views.py uses
                * mapsolarsystems
                * mapregions
            * emtools/corpreport/reporter.py uses
                * mapsolarsystemjumps
            * emtools/ccpeve uses
                * mapsolarsystems
                * mapregions
                * mapdenormalize
                * mapsolarsystemjumps
                * mapconstellations
    * Rubicon 1.0 (Nov 2013)
        * removed certificates, which we never used
        * this was right when Arkady's last changes hit, so anything more recent than this is a candidate for needing updates
    * Retribution 1.0.1 (Dec 2012)
        * removed graphicID and radius from invTypes; do I need to remove these from ccpmodels.py?
        * also iconID, does this affect the other things that have iconID in ccpmodels.py?
        * this was while Arkady was still working on this, so probably okay to ignore

