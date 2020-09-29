## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.<br />
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

DEMO  :   http://mathgoteasy.com/demos/

CODE:

import * as React from "react";

import 'jqwidgets-scripts/jqwidgets/styles/jqx.base.css';

import 'jqwidgets-scripts/jqwidgets/styles/jqx.material-purple.css';

import JqxTreeMap, { ITreeMapProps } from 'jqwidgets-scripts/jqwidgets-react-tsx/jqxtreemap';

import jsondata from "./TreemapData.json";

import 'bootstrap/dist/css/bootstrap.min.css';

///import 'font-awesome/css/font-awesome.min.css';

class TreeMap extends React.PureComponent {




    getChildrenData = function (id) {

        this.state.source.map((data, key) => {

            if (data.label.includes('Go Back')) {

                if (data.record.id === id) {

                    console.log(id, data)

                    this.setState({

                        source: data.record.parent

                    });


                }

            }

            else

                if (data.record.id == id) {

                    console.log(id, this.state.source[key]['record']['id'], data.record.children)

                    if (data.record.children != undefined) {

                        if (!this.state.source[key]['record']['children'][0].label.includes('Go Back')) {


                            this.state.source[key]['record']['children'].unshift({

                                label: `Go Back `,

                                value: 50,

                                color: '#000000',

                                record: {

                                    id: id + 'gb',

                                    ticket: '',

                                    percent: '',

                                    state: '',

                                    parent: this.state.source

                                },




                            })

                        }


                        this.setState({

                            source: data.record.children

                        });

                    }


                }

        })

        this.changeValue();

    };

   changeValue = function(){

    let elems = document.querySelectorAll('.jqx-widget')[0].childNodes;

    let source = [...this.state.source];

    for(let i=0;i<elems.length;i++)

    {

        let isOverflowing = elems[i].clientWidth < elems[i].scrollWidth || elems[i].clientHeight < elems[i].scrollHeight;

        

        if(isOverflowing)

        {  

                    let value = this.state.source[i].value;

                    let item = {...source[i]};    

                    item.value = elems[i].scrollHeight;

                    source[i] = item;

                    this.setState({source:source});

        }

    }

   }

    componentDidMount(){

        

        this.changeValue();

    }

    constructor() {

        super();

        this.state = {

              treeheight:200,

            renderCallbacks: {

                "*": (element, data) => {

                    element.css({

                        color: "#ffffff",

                        border: "1px solid white",

                    });

                   if(data.label.includes('Go Back'))

                   {

                    element.html(
                       '<div style="font-size: 13px;verticalAlign:bottom,bottom:0px" class="grab">' +

                        "<span  style='font-size:15px;'>" +

                        data.label +  `<i class="fa fa-arrow-circle-left" aria-hidden="true"></i>` +

                        "</div>"

                    );

                   }

                   else

                   {

                    element.html(

                        //  getchildren(data.record.id)   onClick="'+getchildren(data.record.id)+'"  this.gettt.bind(this)




                        '<div id='+data.record.id+' style="font-size: 13px;verticalAlign:bottom,bottom:0px" class="grab">' +

                        "<span   style='font-size:15px;color:black;font-weight:bold'>" +

                        data.label +

                        "</span>" +

                        "<br/>" +

                        "<span style='font-size:20px;'>" +

                        data.record.ticket +

                        " " +

                        "</span>" +

                        ` <i class="fa fa-level-down" aria-hidden="true"></i>` + data.record.percent +

                        " " +

                        data.record.state +

                        "</div>"


                    );

                   }


                    element.click(() => this.getChildrenData(data.record.id));

                    

                    return true;

                },




            },

            source: jsondata

        };

    }




    render() {

     return (
            <div>

                <h1>Tree Map</h1>
                <h3>Allocates size based on number you specify</h3>
                <h3>Clicking on any item retireves it's children</h3>

                <JqxTreeMap

                    width={"TreeMap"}

                    style={{ position: 'relative',cursor:'pointer' }}

                    height={this.state.treeheight}

                    source={this.state.source}

                    renderCallbacks={this.state.renderCallbacks}

                />
            </div>

        );

    }

}

export default TreeMap;
