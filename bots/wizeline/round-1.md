You are given a list of API calls in the format /project/subproject/method. You need to calculate and print the number of calls to each node of the API endpoint as a tree.

In this tree, projects, subprojects, and methods should be sorted in the same order as they were given in the input data. The output tree should consist of several strings. All subprojects fall under their parent project, and all methods fall under the subproject in which they are included. The string that represents a project starts with --, while subprojects start with ---- and methods start with ------. After the project, subproject, or method name, put the number of requests to this module in parentheses. Take a look at the example for a guide of what this tree should look like.

Example

For
```js
calls = [
        "/project1/subproject1/method1",
        "/project2/subproject1/method1",
        "/project1/subproject1/method1",
        "/project1/subproject2/method1",
        "/project1/subproject1/method2",
        "/project1/subproject2/method1",
        "/project2/subproject1/method1",
        "/project1/subproject2/method1"
]
```
the output should be
```js
countAPI(calls) = [
        "--project1 (6)",
        "----subproject1 (3)",
        "------method1 (2)",
        "------method2 (1)",
        "----subproject2 (3)",
        "------method1 (3)",
        "--project2 (2)",
        "----subproject1 (2)",
        "------method1 (2)"
]
```
Here, the first mention of project2 was after the first mention of project1, so project1 comes first. In the same way, the first mention of /project1/subproject1 comes before /project1/subproject2, so it comes first in the output.

```js
//solution
function countAPI(calls) {
  let projects = [];
  let projects_map = {};
  let result = [];

  calls.map((call) => {
    let array = call.split('/');
    let project = array[1];
    let subproject = array[2];
    let method = array[3];

    if (typeof projects_map[project] == 'undefined') {
      let pro = {
        id: call,
        name: project,
        count: 1,
        type: 'project'
      }
      projects_map[project] = pro;
      projects.push(pro);

    } else {
      projects_map[project].count++;
      // let pro = projects.find((p)=>{ return p.id == call});
      // if (pro)
      // pro.count++;
    }

    if (typeof projects_map[project][subproject] == 'undefined') {
      let subpro = {
        id: call,
        name: subproject,
        count: 1,
        type: 'subproject',
        // project: project
      };
      projects_map[project][subproject] = subpro;
      projects.push(subpro);
    } else {
      projects_map[project][subproject].count++;
      // let subpro = projects.find((p)=>{ return p.id == call});
      // if (subpro)
      // subpro.count++;
    }

    if (!projects_map[project][subproject][method]) {
      let met = {
        id: call,
        name: method,
        count: 1,
        type: 'method',
        // project: project,
        // subproject: subproject
      };
      projects_map[project][subproject][method] = met;
      projects.push(met)
    } else {
      projects_map[project][subproject][method].count++;
      // let met = projects.find((p)=>{ return p.id == call});
      // if (met)
      // met.count++;
    }


  })


  let _projects = projects.filter((p) => {
    return p.type === 'project';
  })

  _projects.map((pro) => {
    result.push(`--${pro.name} (${pro.count})`);
    // get subprojects
    for (let k in pro) {
      if (k != 'name' && k != 'count' && k != 'type' && k != 'id') {
        // its a subproject
        let subproject = pro[k];
        result.push(`----${subproject.name} (${subproject.count})`);

        // get methods
        for (let k in subproject) {
          if (k != 'name' && k != 'count' && k != 'type' && k != 'id') {
            // its a method
            let method = subproject[k];
            result.push(`------${method.name} (${method.count})`);
          }
        }
      }
    }
  })

  return result;

}

