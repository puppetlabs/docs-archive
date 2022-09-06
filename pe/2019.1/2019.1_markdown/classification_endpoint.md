---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Classification endpoint

Use the `classification` endpoint takes a node name and a set of facts and returns information about how that node should be classified. The output can help you test your classification rules.

## POST /v1/classified/nodes/<name\>

Use the `/v1/classified/nodes/\<name\>` endpoint to retrieve the classification information for the node with the given name and facts as supplied in the body of the request.

### Request format

The request body can contain a JSON object describing the facts and trusted facts of the node to be classified. The object can have these keys:

|Key|Definition|
|---|----------|
|`fact`|The regular, non-trusted facts of the node. The value of this key is a further object, whose keys are fact names, and whose values are the fact values. Fact values can be a string, number, boolean, array, or object.|
|`trusted`|The trusted facts of the node. The values of this key are subject to the same restrictions as those on the value of the `fact` key.|

### Response format

The response is a JSON object describing the node post-classification, using these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node \(a string\).|
|`groups`|An array of the node groups \(UUID strings\) that this node was classified into.|
|`environment`|The name of the environment that this node uses, which is taken from the node groups the node was classified into.|
|`classes`|An object where the keys are class names and the values are objects that map parameter names to values.|
|`parameters`|An object where the keys are top-level variable names and the values are the values assigned to those variables.|

This is an example of a response from this endpoint:

```json
{
  "name": "foo.example.com",
  "groups": ["00000000-0000-4000-8000-000000000000", "08c7915b-b83f-4d11-9522-6a60e2378cef"],
  "environment": "staging",
  "parameters": {},
  "classes": {
    "apache": {
      "keepalive_timeout": 30,
      "log_level": "notice"
    }
  }
}
```

### Error responses

If the node is classified into multiple node groups that define conflicting classifications for the node, the service returns a 500 Server Error response.

The body of this response contains the usual JSON error object described in the errors documentation.

The `kind` key of the error is "classification-conflict", the `msg` describes generally why this happens, and the `details` key contains an object that describes the specific conflicts encountered.

The details object can have these keys:

|Key|Definition|
|---|----------|
|`environment`|Maps directly to an array of value detail objects \(described below\).|
|`variables`|Contains an object with a key for each conflicting variable, whose values are an array of value detail objects.|
|`classes`|Contains an object with a key for each class that had conflicting parameter definitions, whose values are further objects that describe the conflicts for that class's parameters.|

A value details object describes one of the conflicting values defined for the environment, a variable, or a class parameter. Each object contains these keys:

|Key|Definition|
|---|----------|
|`value`|The defined value, which is a string for environment and class parameters, but for a variable can be any JSON value.|
|`from`|The node group that the node was classified into that caused this value to be added to the node's classification. This group might not define the value, because it might be inherited from an ancestor of this group.|
|`defined_by`|The node group that actually defined this value. This is often the `from` group, but could instead be an ancestor of that group.|

This example shows a classification conflict error object with node groups truncated for clarity:

```javascript
{
  "kind": "classification-conflict",
  "msg": "The node was classified into multiple unrelated node groups that defined conflicting class parameters or top-level variables. See `details` for a list of the specific conflicts.",
  "details": {
    "classes": {
      "songColors": {
        "blue": [
          {
            "value": "Blue Suede Shoes",
            "from": {
              "name": "Elvis Presley",
              "classes": {},
              "rule": ["=", "nodename", "the-node"],
              ...
            },
            "defined_by": {
              "name": "Carl Perkins",
              "classes": {"songColors": {"blue": "Blue Suede Shoes"}},
              "rule": ["not", ["=", "nodename", "the-node"]],
              ...
            }
          },
          {
            "value": "Since You've Been Gone",
            "from": {
              "name": "Aretha Franklin",
              "classes": {"songColors": {"blue": "Since You've Been Gone"}},
              ...
            },
            "defined_by": {
              "name": "Aretha Franklin",
              "classes": {"songColors": {"blue": "Since You've Been Gone"}},
              ...
            }
          }
        ]
      }
    }
  }
}
```

In this example, the conflicting "Blue Suede Shoes" value was included in the classification because the node matched the "Elvis Presley" node group \(since that is the value of the "from" key\), but that node group doesn't define the "Blue Suede Shoes" value. That value is defined by the "Carl Perkins" node group, which is an ancestor of the "Elvis Presley" node group, causing the latter to inherit the value from the former. The other conflicting value, "Since You've Been Gone", is defined by the same node group that the node matched.

**Related information**  


[Node classifier errors](node_classifier_errors.md#)

## POST /v1/classified/nodes/<name\>/explanation

Use the `/v1/classified/nodes/\<name\>/explanation` endpoint to retrieve an explanation of how a node is classified by submitting its facts.

### Request format

The body of the request must be a JSON object describing the node's facts. This object uses these keys:

|Key|Definition|
|---|----------|
|`fact`|Describes the regular \(non-trusted\) facts of the node. Its value must be a further object whose keys are fact names, and whose values are the corresponding fact values. Structured facts can be included here; structured fact values are further objects or arrays.|
|`trusted`|Optional key that describes the trusted facts of the node. Its value has exactly the same format as the `fact` key's value.|
|`name`|The node's name from the request's URL is merged into this object under this key.|

This is an example of a valid request body:

```javascript
{
  "fact": {
    "ear-tips": "pointed",
    "eyebrow pitch": "40",
    "hair": "dark",
    "resting bpm": "120",
    "blood oxygen transporter": "hemocyanin",
    "anterior tricuspids": "2",
    "appendices": "1",
    "spunk": "10"
  }
}
```

### Response format

The response is a JSON object that describes how the node would be classified.

-   If the node would be successfully classified, this object contains the final classification.
-   If the classification would fail due to conflicts, this object contains a description of the conflicts.

This response is intended to provide insight into the entire classification process, so that if a node isn't classified as expected, you can trace the deviation.

Classification proceeds in this order:

1.  All node group rules are tested on the node's facts and name, and groups that don't match the node are culled, leaving the matching groups.
2.  Inheritance relations are used to further cull the matching groups, by removing any matching node group that has a descendant that is also a matching node group. Those node groups that are left over are leaf groups.
3.  Each leaf group is transformed into its inherited classification by adding all the inherited values from its ancestors.
4.  All of the inherited classifications and individual node classifications are inspected for conflicts. A conflict occurs whenever two inherited classifications define different values for the environment, a class parameter, or a top-level variable.
5.  Any individual node classification, including classes, class parameters, configuration data, and variables, is added.

6.  Individual node classification is applied to the group classification, forming the final classification, which is then returned to the client.


The JSON object returned by this endpoint uses these keys:

|Key|Definition|
|---|----------|
|`match_explanations`|Corresponds to step 1 of classification, finding the matching node groups. This key's value is an explanation object just like those found in node check-ins, which maps between a matching group's ID and an explained condition object that demonstrates why the node matched that group's rule.|
|`leaf_groups`|Corresponds to step 2 of classification, finding the leaves. This key's value is an array of the leaf groups \(that is, those groups that are not related to any of the other matching groups\).|
|`inherited_classifications`|Corresponds to step 3 of classification, adding inherited values. This key's value is an object mapping from a leaf group's ID to the classification values provided by that group \(after inheritance\).|
|`conflicts`|Corresponds to step 4 of classification. This key is present only if there are conflicts between the inherited classifications. Its value is similar to a classification object, but wherever there was a conflict there's an array of conflict details instead of a single classification value. Each of these details is an object with three keys: `value`, `from`, and `defined_by`. The `value` key is a conflicting value, the `from` key is the group whose inherited classification provided this value, and the `defined_by` key is the group that actually defined the value \(which can be an ancestor of the `from` group\).|
|`individual_classification`|Corresponds to step 5 of classification. Its value includes classes, class parameters, configuration data, and variables applied directly during this stage.|
|`final_classification`|Corresponds to step 6 of classification, present only if there are no conflicts between the inherited classifications. Its value is the result of merging all individual node classification and group classification.|
|`node_as_received`|The submitted node object as received by the service, after adding the `name` and, if not supplied by the client, an empty `trusted` object. This key does not correspond to any of the classification steps.|
|`classification_source`|An annotated version of the classification that has the environment and every class parameter and variable replaced with an "annotated value" object. This key does not correspond to any of the classification steps.|

This example shows a response the endpoint could return in the case of a successful classification:

```javascript
{
  "node_as_received": {
    "name": "Tuvok",
    "trusted": {},
    "fact": {
      "ear-tips": "pointed",
      "eyebrow pitch": "30",
      "blood oxygen transporter": "hemocyanin",
      "anterior tricuspids": "2",
      "hair": "dark",
      "resting bpm": "200",
      "appendices": "0",
      "spunk": "0"
    }
  },
  "match_explanations": {
    "00000000-0000-4000-8000-000000000000": {
      "value": true,
      "form": ["~", {"path": "name", "value": "Tuvok"}, ".*"]
    },
    "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
      "value": true,
      "form": ["and",
               {
                 "value": true,
                 "form": [">=", {"path": ["fact", "eyebrow pitch"], "value": "30"}, "25"]
               },
               {
                 "value": true,
                 "form": ["=", {"path": ["fact", "ear-tips"], "value": "pointed"}, "pointed"]
               },
               {
                 "value": true,
                 "form": ["=", {"path": ["fact", "hair"], "value": "dark"}, "dark"]
               },
               {
                 "value": true,
                 "form": [">=", {"path": ["fact", "resting bpm"], "value": "200"}, "100"]
               },
               {
                 "value": true,
                 "form": ["=",
                          {
                            "path": ["fact", "blood oxygen transporter"],
                            "value": "hemocyanin"
                          },
                          "hemocyanin"
                 ]
               }
      ]
    }
  },
  "leaf_groups": {
    "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
      "name": "Vulcans",
      "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
      "parent": "00000000-0000-4000-8000-000000000000",
      "rule": ["and", [">=", ["fact", "eyebrow pitch"], "25"],
                      ["=", ["fact", "ear-tips"], "pointed"],
                      ["=", ["fact", "hair"], "dark"],
                      [">=", ["fact", "resting bpm"], "100"],
                      ["=", ["fact", "blood oxygen transporter"], "hemocyanin"]
      ],
      "environment": "alpha-quadrant",
      "variables": {},
      "classes": {
        "emotion": {"importance": "ignored"},
        "logic": {"importance": "primary"}
      },
      "config_data": {
        "USS::Voyager": {"designation": "subsequent"}
      }
    }
  },
  "inherited_classifications": {
    "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
      "environment": "alpha-quadrant",
      "variables": {},
      "classes": {
        "logic": {"importance": "primary"},
        "emotion": {"importance": "ignored"}
      },
      "config_data": {
        "USS::Enterprise": {"designation": "original"},
        "USS::Voyager": {"designation": "subsequent"}
      }
    }
  },
  "individual_classification": {
    "classes": {
      "emotion": {
        "importance": "secondary"
      }
    },
    "variables": {
      "full_name": "S'chn T'gai Spock"
    }
  },
  "final_classification": {
    "environment": "alpha-quadrant",
    "variables": {
      "full_name": "S'chn T'gai Spock"
    },
    "classes": {
      "logic": {"importance": "primary"},
      "emotion": {"importance": "secondary"}
    },
    "config_data": {
      "USS::Enterprise": {"designation": "original"},
      "USS::Voyager": {"designation": "subsequent"}
    }
  },
  "classification_sources": {
    "environment": {
      "value": "alpha-quadrant",
      "sources": ["8aeeb640-8dca-4b99-9c40-3b75de6579c2"]
    },
    "variables": {},
    "classes": {
      "emotion": {
        "puppetlabs.classifier/sources": ["8aeeb640-8dca-4b99-9c40-3b75de6579c2"],
        "importance": {
          "value": "secondary",
          "sources": ["node"]
        }
      },
      "logic": {
        "puppetlabs.classifier/sources": ["8aeeb640-8dca-4b99-9c40-3b75de6579c2"],
        "importance": {
          "value": "primary",
          "sources": ["8aeeb640-8dca-4b99-9c40-3b75de6579c2"]
        }
      },
      "config_data": {
        "USS::Enterprise": {
          "designation": {
            "value": "original",
            "sources": ["00000000-0000-4000-8000-000000000000"]
          }
        },
        "USS::Voyager": {
          "designation": {
            "value": "subsequent",
            "sources": ["8aeeb640-8dca-4b99-9c40-3b75de6579c2"]
          }
        }
      }
    }
  }
}
```

This example shows a response that resulted from conflicts:

```javascript
{
  "node_as_received": {
    "name": "Spock",
    "trusted": {},
    "fact": {
      "ear-tips": "pointed",
      "eyebrow pitch": "40",
      "blood oxygen transporter": "hemocyanin",
      "anterior tricuspids": "2",
      "hair": "dark",
      "resting bpm": "120",
      "appendices": "1",
      "spunk": "10"
    }
  },
  "match_explanations": {
    "00000000-0000-4000-8000-000000000000": {
      "value": true,
      "form": ["~", {"path": "name", "value": "Spock"}, ".*"]
    },
    "a130f715-c929-448b-82cd-fe21d3f83b58": {
      "value": true,
      "form": [">=", {"path": ["fact", "spunk"], "value": "10"}, "5"]
    },
    "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
      "value": true,
      "form": ["and",
               {
                 "value": true,
                 "form": [">=", {"path": ["fact", "eyebrow pitch"], "value": "30"}, "25"]
               },
               {
                 "value": true,
                 "form": ["=", {"path": ["fact", "ear-tips"], "value": "pointed"}, "pointed"]
               },
               {
                 "value": true,
                 "form": ["=", {"path": ["fact", "hair"], "value": "dark"}, "dark"]
               },
               {
                 "value": true,
                 "form": [">=", {"path": ["fact", "resting bpm"], "value": "200"}, "100"]
               },
               {
                 "value": true,
                 "form": ["=",
                          {
                            "path": ["fact", "blood oxygen transporter"],
                            "value": "hemocyanin"
                          },
                          "hemocyanin"
                 ]
               }
      ]
    }
  },
  "leaf_groups": {
                   "a130f715-c929-448b-82cd-fe21d3f83b58": {
                     "name": "Humans",
                     "id": "a130f715-c929-448b-82cd-fe21d3f83b58",
                     "parent": "00000000-0000-4000-8000-000000000000",
                     "rule": [">=", ["fact", "spunk"], "5"],
                     "environment": "alpha-quadrant",
                     "variables": {},
                     "classes": {
                       "emotion": {"importance": "primary"},
                       "logic": {"importance": "secondary"}
                     }
                   },
                   "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
                     "name": "Vulcans",
                     "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
                     "parent": "00000000-0000-4000-8000-000000000000",
                     "rule": ["and", [">=", ["fact", "eyebrow pitch"], "25"],
                                     ["=", ["fact", "ear-tips"], "pointed"],
                                     ["=", ["fact", "hair"], "dark"],
                                     [">=", ["fact", "resting bpm"], "100"],
                                     ["=", ["fact", "blood oxygen transporter"], "hemocyanin"]
                     ],
                     "environment": "alpha-quadrant",
                     "variables": {},
                     "classes": {
                       "emotion": {"importance": "ignored"},
                       "logic": {"importance": "primary"}
                     }
                   }
  },
  "inherited_classifications": {
    "a130f715-c929-448b-82cd-fe21d3f83b58": {
      "environment": "alpha-quadrant",
      "variables": {},
      "classes": {
        "logic": {"importance": "secondary"},
        "emotion": {"importance": "primary"}
      }
    },
    "8aeeb640-8dca-4b99-9c40-3b75de6579c2": {
      "environment": "alpha-quadrant",
      "variables": {},
      "classes": {
        "logic": {"importance": "primary"},
        "emotion": {"importance": "ignored"}
      }
    }
  },
  "conflicts": {
    "classes": {
      "logic": {
        "importance": [
                        {
                          "value": "secondary",
                          "from": {
                            "name": "Humans",
                            "id": "a130f715-c929-448b-82cd-fe21d3f83b58",
                            ...
                          },
                          "defined_by": {
                            "name": "Humans",
                            "id": "a130f715-c929-448b-82cd-fe21d3f83b58",
                            ...
                          }
                        },
                        {
                          "value": "primary",
                          "from": {
                            "name": "Vulcans",
                            "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
                            ...
                          },
                          "defined_by": {
                            "name": "Vulcans",
                            "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
                            ...
                          }
                        }
        ]
      },
      "emotion": {
        "importance": [
                        {
                          "value": "ignored",
                          "from": {
                            "name": "Vulcans",
                            "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
                            ...
                          },
                          "defined_by": {
                            "name": "Vulcans",
                            "id": "8aeeb640-8dca-4b99-9c40-3b75de6579c2",
                            ...
                          }
                        },
                        {
                          "value": "primary",
                          "from": {
                            "name": "Humans",
                            "id": "a130f715-c929-448b-82cd-fe21d3f83b58",
                            ...
                          },
                          "defined_by": {
                            "name": "Humans",
                            "id": "a130f715-c929-448b-82cd-fe21d3f83b58",
                            ...
                          }
                        }
        ]
      }
    }
  },
  "individual_classification": {
    "classes": {
      "emotion": {
        "importance": "secondary"
      }
    },
    "variables": {
      "full_name": "S'chn T'gai Spock"
    }
  }
}
```

