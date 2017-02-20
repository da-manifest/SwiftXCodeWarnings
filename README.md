# SwiftXCodeWarnings
From: https://krakendev.io/blog/generating-warnings-in-xcode

Navigate to your project's build phases in Xcode, click on the + sign towards the top left of the window, and then click on "New Run Script Phase" in the drop down menu.

You should then see a new section added where you can inject your bash script.

In the body of your run script build phase, copy and paste this lovely bit of code:

TAGS="TODO:|FIXME:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" -or -name "*.swift" \) -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($TAGS).*\$" | perl -p -e "s/($TAGS)/ warning: \$1/"

From here on out, you should now see warnings when you mark your code with TODO: or FIXME: comment blocks!

To take this even one step further, we can edit this script to also highlight errors using an //ERROR: comment block. To do this, change your bash script in your run script build phase to say this:

TAGS="TODO:|FIXME:"
ERRORTAG="ERROR:"
find "${SRCROOT}" \( -name "*.h" -or -name "*.m" -or -name "*.swift" \) -print0 | xargs -0 egrep --with-filename --line-number --only-matching "($TAGS).*\$|($ERRORTAG).*\$" | perl -p -e "s/($TAGS)/ warning: \$1/" | perl -p -e "s/($ERRORTAG)/ error: \$1/"
