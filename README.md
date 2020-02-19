# ContentDM Migration Module Postprocessing Utilities
This repo contains 
- MODS mappings for use with [XML Based CONTENTdm Migration](https://github.com/discoverygarden/xml_based_contentdm_migration) module.
- Sample xsl processes for altering the standard output behavior of the [XML Based CONTENTdm Migration](https://github.com/discoverygarden/xml_based_contentdm_migration) module.

Familiarity with use of the aforementioned module is assumed.

The supplied processes can be forked, modified, combined, or chained with other custom post processing scripts as needed to suit individual use cases.

# MODS Processes
| xsl | Functions |
| ---------- | ---------- |
| `cdm-mods-final-level-to-compounds.xsl` | Alters standard crosswalk handling of hierarchical compounds (into a hierarchy of collections and children) by changing any (sub)collection containing only image objects (no subcollections) into a compound object. This can be modified to be less selective, i.e. create compounds with arbitrary non-collection cModel types. This should usually be use with `cdm-mods-updates-single-children.xsl` following. Requires passing in $islandora-namespace parameter. |
| `cdm-mods-updates-single-children.xsl` | When a compound object has only one child, this removes the parent object, moves the parent metadata to the child, and assigns the child to the collection the parent was a member of. |
| `cdm-mods-final-level-to-books.xsl` | Alters standard crosswalk handling of hierarchical compounds by changing any (sub)collection containing only large image objects (no subcollections) into a book, and its children into pages. Requires passing in $islandora-namespace parameter. |
| `cdm-mods-final-level-to-newspapers.xsl` | Alters standard crosswalk handling of hierarchical compounds by changing any (sub)collection containing only large image objects (no subcollections) into a newspaper issue, and its children into newspaper pages. Requires passing in $islandora-namespace parameter. |
| `split-mods-files.xsl` | For handling very large files it is sometimes required to split output files into smaller batches. Set the `$object-per-file` param to the number of objects per file. Note this stylesheet does use a saxon extension functions, and therefore has a dependency on it. |

# Sequencing of Processes
Transformations that don't alter content model choice can generally be run at any point in a processing chain unless the content model is being used as a condition. Transformations that _do_ alter content model must be run in a specific sequence or results will not be as desired. Guidance on the order of these transforms is as follows:

- If all objects in a collection are compounds and any transformation is being used to change the content model of the final level in a hierarchical structure, e.g. `cdm-mods-final-level-to-books.xsl`, that transformation should be run first except under the following condition. 
- If books are being created selectively, or there are both standalone and aggregate objects in a collection, any transformation to change the content of the final level in a hierarchical structure is run _following_ `cdm-mods-updates-single-children.xsl`
- In any circumstance, if final or remaining final levels of the collections/object hierarchy are being transformed into compounds using `cdm-mods-final-level-to-compounds.xsl`, that is run _following_ the transform to create any other type of aggregate object such as books, newspapers.
- The update to remove parent objects from single children compounds, `cdm-mods-updates-single-children.xsl` is run last if no alteration to content models is being affected.

No warranty is implied. Users are solely responsible for testing and verify all results.
